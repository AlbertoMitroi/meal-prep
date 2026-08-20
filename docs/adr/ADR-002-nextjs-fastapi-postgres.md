# ADR-002 — Next.js + FastAPI + PostgreSQL

**Status:** Accepted

## Context

The product needs a mobile-first web UI and a backend that will increasingly contain numerical logic, optimization, data processing and AI integration.

## Decision

Use Next.js/React/TypeScript for the frontend, Python/FastAPI/Pydantic/SQLAlchemy for the application backend, and PostgreSQL for canonical persistence.

## Consequences

Strong fit for both UI iteration and future Python solver/AI ecosystem. Requires maintaining an HTTP contract between frontend and backend.

## Alternatives considered

A single TypeScript full-stack application was simpler initially but weaker for the planned Python-heavy engines. Native mobile is deferred until native requirements justify it.

## References

- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`
