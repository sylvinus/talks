# Messages: how a French government agency broke free of IMAP

- **Event:** [FOSDEM 2026](https://fosdem.org/2026/) — Modern Email devroom
- **Date:** Saturday, January 31, 2026, 15:00–15:30
- **Location:** Room K.4.201, ULB Solbosch Campus, Brussels, Belgium

## Description

How the French Agence Nationale de la Cohesion des Territoires (ANCT) built [Messages](https://github.com/suitenumerique/messages), an open source email platform that drops IMAP entirely in favor of an OIDC-only, collaboration-first architecture. Covers the design choices (JMAP-inspired data model, stateless SMTP, S3 object storage), the simplified architecture with minimal stateful components, and per-recipient delivery statuses.

## Files

- [slides.md](slides.md) — Slidev source
- [FOSDEM 2026 - Sylvain Zimmer - Messages.pdf](FOSDEM%202026%20-%20Sylvain%20Zimmer%20-%20Messages.pdf) — Exported PDF
- [style.css](style.css) — Custom theme
- [images/](images/) — Slide assets
- [Makefile](Makefile) — Build/dev commands

## Building

```bash
make dev    # start dev server on port 3030
make pdf    # export to PDF
```

## Links

- Schedule: <https://fosdem.org/2026/schedule/event/CNMT8K-messages_how_a_french_government_agency_broke_free_of_imap/>
- Project: <https://github.com/suitenumerique/messages>
- Matrix: [#messages-official:matrix.org](https://matrix.to/#/#messages-official:matrix.org)
