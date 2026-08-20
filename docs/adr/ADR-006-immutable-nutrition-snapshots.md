# ADR-006 — Immutable Historical Nutrition Snapshots

**Status:** Accepted

## Context

Food/product nutrition definitions can change over time. Historical consumption must preserve the values used when the food was actually logged.

## Decision

Every confirmed FoodLog references a NutritionSnapshot calculated at log time. Later catalog changes do not mutate historical truth; corrections are explicit operations.

## Consequences

Stable history, reproducible analytics and safe future learning/adaptation.

## Alternatives considered

Recalculating all history from current food definitions was rejected because it silently rewrites the past.

## References

- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`
