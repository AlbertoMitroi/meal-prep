# ADR-001 — Modular Monolith

**Status:** Accepted

## Context

The product contains several strongly related domains—nutrition, meals, planning, pantry, shopping, learning and AI—but the domains are still evolving and interact transactionally.

## Decision

Build one deployable FastAPI application and one PostgreSQL database while enforcing clear module ownership and framework-free core engines.

## Consequences

Simpler deployment and transactions; easy refactoring while product knowledge changes; modules can be extracted later only if operational needs justify it.

## Alternatives considered

Microservices were rejected because they would introduce network boundaries, distributed transactions and deployment overhead before scale/ownership requires them.

## References

- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`
