# Engineering Standards

## Purpose

This document defines implementation conventions for the codebase.

It does not redefine product behavior. Product truth remains in `docs/product/`.

The governing principle is:

> Use the simplest implementation that respects the final domain boundaries.

---

## 1. General engineering principles

### 1.1 Prefer explicit code

Prefer code that makes ownership, state changes and error handling obvious.

Avoid framework magic when a small explicit function/service is easier to understand and test.

### 1.2 Do not speculate

Do not create abstractions, infrastructure, modules or extension points solely because they may be useful in a future iteration.

Create the boundary now only when the current implementation needs the concept.

### 1.3 Preserve domain language

Use the canonical vocabulary from `docs/product/06-DOMAIN-MODEL.md`.

Do not create competing names for established concepts.

Examples:

- `Member`, not `ProfileUser` for the nutritional person;
- `FoodLog`, not `CompletedMealRecord`;
- `NutritionSnapshot`, not `CaloriesCache`.

### 1.4 One owner per truth

Every persistent concept must have one canonical owner.

Derived/read models may duplicate presentation data, but they are not authoritative.

---

## 2. Repository organization

The codebase is a modular monolith.

Top-level application areas:

```text
apps/web
apps/api
packages/api-client
docs
infra
scripts
```

Backend module boundaries follow the product/domain modules.

Frontend code is feature-first.

Do not create empty future modules.

---

## 3. Python backend standards

### 3.1 Stack

Use:

- Python;
- FastAPI;
- Pydantic;
- SQLAlchemy 2;
- Alembic;
- pytest;
- Ruff;
- Pyright;
- `uv`.

### 3.2 Route handlers are thin

FastAPI route handlers should:

1. parse/validate input;
2. resolve actor/context;
3. call an application service/query;
4. serialize the result.

Do not place nutrition calculations, orchestration or persistence workflows directly in routes.

### 3.3 Engines are framework-free

Core engines/solvers must not import:

- FastAPI;
- SQLAlchemy;
- Supabase;
- HTTP clients;
- deployment/provider SDKs.

Engines accept structured input and return structured output.

### 3.4 Application services own use-case orchestration

Application services coordinate:

```text
authorization
→ loading data
→ building engine input
→ running domain logic
→ persistence
→ events/invalidation
```

### 3.5 Persistence is internal to the owning module

Another module must not reach into a module's SQLAlchemy implementation as its public API.

Prefer public queries/services or a purpose-built context builder.

### 3.6 Type everything important

Domain inputs/outputs, service contracts and API schemas should be strongly typed.

Avoid broad `dict[str, Any]` in domain logic.

Flexible/raw external payloads may use JSON structures at adapter boundaries.

### 3.7 Pydantic and SQLAlchemy remain conceptually separate

Do not force a single "magic model" to be API schema, domain object and ORM entity simultaneously.

### 3.8 Error handling

Use explicit domain/application error codes.

Do not leak:

- SQLAlchemy errors;
- stack traces;
- provider exceptions;

to clients.

### 3.9 Logging

Use structured logging.

Log identifiers needed for debugging, but do not log unnecessary personal/health-adjacent content.

---

## 4. TypeScript / Next.js standards

### 4.1 Stack

Use:

- Next.js App Router;
- React;
- TypeScript;
- Tailwind CSS;
- shadcn/ui;
- TanStack Query for server state.

### 4.2 Feature-first structure

Domain-aware UI stays inside its feature.

Examples:

```text
features/today
features/progress
features/household
```

Generic primitives belong in shared UI components.

### 4.3 Canonical calculations never live in the frontend

The frontend must not calculate:

- canonical nutrition;
- nutrition targets;
- nutrient gaps;
- portion solutions;
- recommendation scores;
- weekly planning decisions.

Presentation-only calculations are fine.

### 4.4 Prefer server state over global client stores

Remote application state belongs to TanStack Query.

Local UI state belongs to React.

Do not add Redux/Zustand by default.

### 4.5 API types are generated

Use the generated OpenAPI TypeScript client.

Do not manually duplicate backend request/response schemas.

### 4.6 Mobile first

Design and implement primary flows first for approximately 375–430 px widths.

Desktop should expand gracefully, not become a separate dashboard product unless required.

---

## 5. UX implementation standards

`docs/product/08-UX-FLOWS.md` is canonical.

Mandatory implementation rules:

- one clear primary action per screen/state;
- progressive disclosure;
- no future disabled tabs/features;
- common actions stay short;
- do not ask for known information again;
- advanced capability stays available without dominating default UI;
- error states always offer a next action;
- do not use shame/punishment language;
- do not use red for ordinary nutrition deviation;
- estimated data must not look exact.

Loading, empty and error states are part of the feature, not post-launch polish.

---

## 6. Database standards

### 6.1 PostgreSQL is canonical persistence

Integration behavior must be tested against PostgreSQL, not SQLite.

### 6.2 Alembic only

All schema evolution uses migrations.

### 6.3 IDs

Use stable UUIDs for domain entities unless there is a compelling reason otherwise.

### 6.4 Time

Use timezone-aware timestamps for moments.

Use semantic dates for local-day concepts.

Nutrition-day boundaries are calculated using the household timezone.

### 6.5 Money

Use `Decimal` / PostgreSQL `NUMERIC`.

Never use binary floating point as money truth.

### 6.6 Unknown is not zero

Missing nutrition values are `NULL`/unknown.

Never convert unavailable nutrient data to zero.

### 6.7 Raw/cooked state

Food quantities that depend on preparation must preserve preparation state.

### 6.8 Historical snapshots

Historical consumed nutrition remains immutable except via an explicit correction operation.

### 6.9 Constraints

Use database constraints for simple invariants where practical, while keeping business behavior visible in Python.

### 6.10 Triggers

Avoid implementing core business/nutrition logic in database triggers.

---

## 7. External provider boundaries

External systems are adapters, not domain models.

Examples:

- auth provider;
- nutrition/product APIs;
- object storage;
- AI providers;
- analytics.

Normalize external data before domain use.

Do not expose provider-specific fields throughout the application.

---

## 8. Dependency policy

A new dependency must have a current, concrete use.

Before adding one, document in the PR/task:

- problem being solved;
- why standard library/existing stack is insufficient;
- operational cost;
- replacement/removal implications if meaningful.

An ADR is required when the dependency materially changes architecture.

---

## 9. Comments and documentation

Prefer expressive code over comments that narrate syntax.

Comments should explain:

- non-obvious invariant;
- scientific/business reasoning;
- compatibility constraint;
- intentional trade-off.

Public engine/service contracts should have concise docstrings where the contract is not self-evident.

---

## 10. Refactoring discipline

Refactor within task scope when needed for correctness/clarity.

For unrelated refactors:

- record technical debt;
- do not expand the active task casually.

If a task repeatedly fights existing architecture, stop and evaluate whether an ADR/product-doc change is necessary.

---

## 11. Generated code

Generated artifacts must be clearly separated and reproducible.

Do not hand-edit generated API client output.

The generator command must be documented and CI must detect drift.

---

## 12. Architecture smells

Pause and review if you see:

- business logic duplicated in Python and TypeScript;
- frontend table access bypassing FastAPI;
- a route handler coordinating many repositories directly;
- an engine performing DB/network calls;
- a giant cross-domain service;
- one JSON blob replacing queryable domain entities;
- provider response types leaking through core modules;
- future modules/infrastructure with no current callers;
- repeated `if iteration_x` branching in core domain logic.
