# ADR-004 — Household-First Domain Model

**Status:** Accepted

## Context

The product must support individuals, couples and households with shared meals/logistics but individual nutrition targets and history.

## Decision

Treat an individual as a household of one. Keep Account, Household and Member distinct from the first iteration.

## Consequences

Avoids later migration from `user_id on everything`; supports members without accounts and clean shared ownership.

## Alternatives considered

A user-centric model was rejected because it would duplicate household resources and make multi-member planning a retrofit.

## References

- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`
