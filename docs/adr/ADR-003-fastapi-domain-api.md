# ADR-003 — FastAPI Owns the Domain API

**Status:** Accepted

## Context

Managed infrastructure such as Supabase can expose database APIs directly, but allowing both frontend→DB and frontend→FastAPI paths would split authorization and business rules.

## Decision

All application/domain operations from the frontend go through FastAPI. Supabase may provide Auth/Postgres/Storage as infrastructure, not a second application logic path.

## Consequences

One application authority; easier audit, authorization, testing and future engine integration.

## Alternatives considered

Direct browser access to application tables was rejected. RLS may still be used as defense-in-depth where tables are exposed.

## References

- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`
