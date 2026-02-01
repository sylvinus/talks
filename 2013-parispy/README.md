# Why and how Pricing Assistant migrated from Celery to RQ

- **Event:** [Paris.py #2](https://www.parispy.org/)
- **Date:** July 22, 2013
- **Location:** Paris, France

## Description

The story of migrating Pricing Assistant's task queue infrastructure from Celery to RQ (Redis Queue), covering the motivations, migration process, and lessons learned. Celery (~27k lines of code) was replaced by RQ (~2k lines), then soft-forked into MRQ. This experience later led to building [MRQ](https://github.com/pricingassistant/mrq), a custom task queue combining the best of both.

## Files

- [slides.pdf](slides.pdf)
