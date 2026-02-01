---
theme: default
title: "Messages: how a French government agency broke free of IMAP"
info: |
  FOSDEM 2026 - Modern Email Track
  Sylvain Zimmer, ANCT
class: text-center
drawings:
  persist: false
mdc: true
aspectRatio: 16/9
canvasWidth: 980
---

<style>
@import './style.css';
</style>

# Messages

## How a French government agency broke free of IMAP

FOSDEM 2026 - Modern Email devroom

Sylvain Zimmer - January 31, 2026

<div style="position: absolute; bottom: 40px; left: 0; right: 0; display: flex; justify-content: center; gap: 60px; align-items: center;">
  <a href="https://anct.gouv.fr" style="text-decoration: none;"><img src="/images/logo_anct.png" style="height: 60px;" /></a>
  <a href="https://lasuite.numerique.gouv.fr" style="text-decoration: none;"><img src="/images/logo_suite_territoriale.svg" style="height: 50px;" /></a>
</div>

---

# About me

**Sylvain Zimmer** - CTO @ La Suite territoriale

- Co-founder: Jamendo, dotConferences, TEDxParis, Pricing Assistant
- 20 years of development, conferences & free software advocacy
- [Hired](https://sylvainzimmer.com/blog/2025/01/public-sector/) by DINUM in 2025 as [Entrepreneur d'Intérêt Général](https://eig.numerique.gouv.fr/), sent to ANCT
- Languages: Python, Go, TypeScript, Markdown?!

&nbsp;

GitHub: [sylvinus](https://github.com/sylvinus)

Blog: [sylvainzimmer.com](https://sylvainzimmer.com)


---

<img src="/images/dmarc-quarantine-cpnt.png" style="width: 100%; height: 100%; object-fit: contain; position: absolute; top: 0; left: 0; right: 0; bottom: 0;" />

---

# ANCT & La Suite territoriale

**ANCT** = Agence Nationale de la Cohésion des Territoires

We help **35,000+ local governments** go digital, mostly small towns below 3,500 inhabitants.

&nbsp;

**[La Suite territoriale](https://suiteterritoriale.anct.gouv.fr/)** is our open source workspace for their public workers:

- Part of a broader movement with DINUM's **LaSuite** (for State workers)
- Includes [SSO](https://proconnect.gouv.fr/), [Messages](https://github.com/suitenumerique/messages), [Drive](https://github.com/suitenumerique/drive), [Projects](https://github.com/suitenumerique/projects), [Grist](https://github.com/gristlabs/grist-core), [Visio](https://github.com/suitenumerique/meet), [Docs](https://github.com/suitenumerique/docs) & more!
- Self-hosting strongly encouraged at regional level.
- MIT licensed

&nbsp;

***Let's just take a moment to celebrate OSS becoming the default for EU public digital services!***

---

<img src="/images/messages-inbox.png" style="width: 100%; height: 100%; object-fit: contain; position: absolute; top: 0; left: 0; right: 0; bottom: 0;" />

---

# Design choices for Messages

| Functional need | Technical solution |
|----------------|-------------------|
| Zero passwords | **OIDC-only**, no IMAP, no client SMTP |
| Shared mailboxes | Collaboration-ready **data model** |
| [LaSuite UI Kit](https://github.com/suitenumerique/ui-kit) | Build **custom frontend** |
| Cost efficiency | S3-compatible **object storage** for emails ([#486](https://github.com/suitenumerique/messages/pull/486)) |
| Easy self-hosting | Minimize **stateful** components, make most others optional |
| OSS community | Accept different choices behind **config flags**, when relevant |

---

# Dropping IMAP

Legacy auth weakens security. Protocol constrains data models.

Dropping IMAP was a foundational step. It meant:

- No folders, no flags, fewer MIME quirks, no persistent connections, no dialects
- Freedom to build a collaboration-first data model, with **Threads** as native objects
- Strong argument to migrate users off **proprietary** clients (you know which one)
- Ability to build a **tightly coupled frontend** with unique features:
  - [Blocknote.js](https://www.blocknotejs.org/) composer, shared with Docs from LaSuite
  - LLMs for summaries, auto-labels, assistant in composer ([#309](https://github.com/suitenumerique/messages/pull/309))
  - Rich threads with private messages & custom metadata  ([#383](https://github.com/suitenumerique/messages/pull/383))
  - Realtime collaboration (?)

---

# JMAP shaped our data model

JMAP Mail's [RFC 8621](https://datatracker.ietf.org/doc/html/rfc8621) was invaluable!

**Blobs**: raw data is content-addressed, deduplicated, stored separately

**Threads**: first-class object, not reconstructed from headers

**Body structure**: use the spec's multipart algorithm for `textBody[]` / `htmlBody[]` / `attachments[]`

**Recipients**: structured `to` / `cc` / `bcc` with contact references

&nbsp;

We have a prototype read/write JMAP endpoint: [#479](https://github.com/suitenumerique/messages/pull/479)

It will be restricted to server-to-server API calls for our deployment, but others might find it useful!

---


# Architecture (simplified)

<img src="/images/architecture-simplified.svg" style="max-height: 420px; margin: 0 auto; display: block; margin-top:33px;" />

---

# Architecture (full)

<img src="/images/architecture.svg" style="max-height: 420px; margin: 0 auto; display: block;" />

---

# Minimal state
PostgreSQL & Object Storage are the only **stateful** components.

<img style="float:right; width:200px;" src="/images/stateful-components.png" />

It's a big deal for:

- **Self-hosting**: less to backup/manage, fewer filesystems involved
- **Scalability**: all the other components are *stateless* and scale horizontally
- **Reliability**: stateless components can crash and restart without data loss
- **Maintainability**: stateless components are easier to swap and refactor

&nbsp;

*Note: Object storage is actually optional, the whole system can run on a single Postgres!*

---

# Stateless SMTP: inbound

In-session validation & delivery to Postgres queue.


<img src="/images/inbound-flow.svg" style="max-height: 420px; margin: -20px auto; display: block;" />


---

# Stateless SMTP: outbound

Direct MX delivery (optional SOCKS proxy) to get per-recipient statuses

```python
statuses = send_message_via_mx(envelope_from, recipients, mime_data)
# => {"alice@example.com": {"delivered": True},
#     "bob@invalid.test": {"delivered": False, "error": "550 User unknown"}}
```

We can then show statuses in the frontend and allow users to retry or dismiss selectively ([#513](https://github.com/suitenumerique/messages/pull/513))

<table>
  <tr style="border:0 !important;">
    <td>
      <img src="/images/delivery-pending.png" style="max-height: 420px; margin: -20px auto; display: block;" />
    </td><td>
      <img src="/images/delivery-failure.png" style="max-height: 420px; margin: -20px auto; display: block;" />
    </td>
  </tr>
</table>

Sorry, mailer-daemon!

---

# What's next
Deploy, deploy, deploy

This talk only included features **in production** or in open [Pull Requests](https://github.com/suitenumerique/messages/pulls) so far!

We are **currently building**: Mobile app (React Native), Calendars, Contacts...

We are also helping partners like [mosa.cloud](https://mosa.cloud/) self-host and contribute. [Contact us](mailto:contact@suite.anct.gouv.fr) if interested.

**The future is bright!**

<table>
  <tr style="border:0 !important;">
    <td>
      <img src="/images/dc-edic.png" style="max-height: 220px; margin: -20px auto; display: block;" />
    </td><td>
      <img src="/images/nyt-visio.png" style="max-height: 220px; margin: -20px auto; display: block;" />
    </td>
  </tr>
</table>

---

# Get involved

[github.com/suitenumerique/messages](https://github.com/suitenumerique/messages) (MIT)

Matrix: [#messages-official:matrix.org](https://matrix.to/#/#messages-official:matrix.org)

Email: [contact@suite.anct.gouv.fr](mailto:contact@suite.anct.gouv.fr)

Feel free to share feedback...

Are we crazy? Anything we missed? A great feature idea? Want to self-host?

**Find me this weekend, I'll be happy to chat!**

## Thanks!
