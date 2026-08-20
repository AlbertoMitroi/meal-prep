# ADR-007 — Infrastructure Is Added Just in Time

**Status:** Accepted

## Context

The long-term product may need workers, Redis, object storage, AI providers, embeddings and advanced solvers, but Iteration 0 does not.

## Decision

Add infrastructure only when a shipped capability has a concrete need. Preserve clean interfaces so later infrastructure can be attached without pre-building it.

## Consequences

Lower operational cost and less speculative complexity while keeping architectural growth paths.

## Alternatives considered

Pre-creating workers, Redis, microservices, vector databases or OR-Tools scaffolding was rejected as premature.

## References

- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`
