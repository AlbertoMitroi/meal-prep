---
name: implement-backend-module
description: Implement FastAPI modular-monolith backend behavior with thin routes, explicit application services, framework-free engines and module-owned persistence.
---

# Implement Backend Module

## Boundaries

Route:
`validation/auth → application service/query → response`

Application service:
`authorize → load → build domain input → execute → persist → events/invalidation`

Engine:
`structured input → deterministic logic → structured output`

Repository:
persistence only.

## Mandatory rules

- No business logic in FastAPI route handlers.
- No SQLAlchemy/FastAPI/provider imports in core engines.
- A module does not use another module's internal ORM repository as public API.
- Authorization is explicit for every member/household mutation/read.
- Use Pydantic for validated contracts and SQLAlchemy for persistence; do not force one model to do every job.
- Use stable domain error codes.
- Preserve transaction boundaries.
- External/provider payloads are normalized at adapters.
- Avoid giant generic services.

## Before adding an abstraction

Ask:

1. Does the current use case need it?
2. Is there already a repository pattern?
3. Does it preserve the module map/domain ownership?
4. Can a simple function/service solve it?

## Verification

- unit tests for pure logic;
- PostgreSQL integration tests for persistence/transactions;
- API tests for auth/validation/error contract;
- generated OpenAPI client updated when schemas change.
