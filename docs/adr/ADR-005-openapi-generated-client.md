# ADR-005 — Generated TypeScript API Client

**Status:** Accepted

## Context

Python/Pydantic and TypeScript can drift if request/response contracts are manually duplicated.

## Decision

Treat FastAPI OpenAPI as canonical and generate the TypeScript client/types used by Next.js. CI detects generated-client drift.

## Consequences

Single contract source and compile-time frontend feedback on API changes.

## Alternatives considered

Manual shared DTO maintenance and GraphQL were rejected as unnecessary complexity for the current architecture.

## References

- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`
