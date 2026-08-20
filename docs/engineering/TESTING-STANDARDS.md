# Testing Standards

## Purpose

Testing exists to protect domain correctness, UX-critical workflows, data integrity and safe iteration.

The goal is not maximum test count. The goal is high confidence at the correct boundary.

---

## 1. Testing matrix

### Pure engine/domain calculation

Use fast unit tests.

Examples:

- nutrition calculation;
- recipe scaling;
- unit conversion;
- future target/portion/recommendation solvers.

No database or HTTP.

### Application service

Use integration tests where persistence/transactions/authorization matter.

Examples:

- consume a plan meal;
- update nutrition targets;
- add household member.

### Repository / persistence behavior

Use PostgreSQL integration tests.

Do not substitute SQLite.

### API contract / endpoint

Use FastAPI/HTTP integration tests.

Verify:

- auth;
- validation;
- status/error contract;
- response schema.

### Frontend component behavior

Use component tests for meaningful UI logic.

### Critical user journey

Use Playwright E2E.

---

## 2. Core principles

### 2.1 Test behavior, not implementation trivia

Prefer:

> consuming a meal produces one FoodLog and correct nutrition

over:

> service called repository method exactly twice.

### 2.2 Protect invariants

Important invariants deserve direct tests.

Examples:

- unknown nutrient is not zero;
- cross-household access is denied;
- historical nutrition snapshot does not silently change;
- duplicate slot consumption is prevented;
- local-day grouping respects household timezone.

### 2.3 Every important bug should become harder to reintroduce

When fixing a meaningful bug, add a regression test at the lowest useful level.

### 2.4 Deterministic core tests

Core nutrition/solver tests must not depend on:

- current clock without injection;
- random values without fixed seed;
- live external APIs;
- AI model responses.

---

## 3. Backend unit tests

Expected for pure logic.

Iteration 0 examples:

- nutrient amount from per-100g source;
- sum nutrition vectors;
- scale recipe to 50/75/125%;
- ingredient adjustment recalculation;
- correct display rounding boundaries;
- missing required V0 macro data rejected by seed validation.

Keep these tests fast enough to run continuously.

---

## 4. PostgreSQL integration tests

Use a real PostgreSQL test database/container.

Test:

- migrations;
- relationships;
- unique/check constraints;
- transaction behavior;
- timezone/date queries;
- JSONB behavior where used;
- authorization-scoped queries.

Each test should isolate its data.

---

## 5. API tests

Test both happy and failure paths.

Examples:

- unauthorized request;
- actor cannot access another household;
- invalid quantity;
- recipe not allowed for plan slot;
- duplicate consumption;
- target update versions active profile correctly.

API tests should assert stable error codes, not internal exception text.

---

## 6. Frontend tests

Component tests should focus on meaningful interaction logic.

Examples:

- pending vs consumed meal slot;
- target-less vs target-aware nutrition summary;
- adjustment sheet values;
- weight input validation;
- loading/empty/error states.

Do not snapshot-test large UI trees by default.

---

## 7. E2E policy

E2E is required for critical product flows, not every button.

Iteration 0 minimum:

1. new account → onboarding → Today;
2. choose plan option → log meal → totals update → refresh persists;
3. adjust to 75% → nutrition reflects change;
4. log meal → undo → totals revert;
5. add weight → Progress reflects it;
6. second member → switch member → independent data.

Run E2E against an environment that resembles production architecture.

---

## 8. Accessibility verification

For user-facing tasks, verify:

- semantic interactive elements;
- labels;
- focus behavior;
- keyboard usability where applicable;
- sufficient contrast;
- states not communicated only by color.

Critical flows should include automated accessibility checks where practical, but manual inspection remains necessary.

---

## 9. Seed/data tests

Nutrition and coach-plan seed data are executable product inputs.

Tests must verify:

- stable codes are unique;
- referenced foods exist;
- active recipes have required quantities;
- preparation state is explicit where required;
- required V0 nutrients exist;
- no invented placeholder values are treated as truth;
- every active recipe nutrition can be calculated;
- plan slots reference valid recipes;
- seed operation is idempotent.

---

## 10. Migration tests

At minimum:

```text
empty PostgreSQL
→ alembic upgrade head
```

must pass in CI.

For non-trivial production migrations, also test:

```text
previous schema/data fixture
→ new migration
→ application reads/writes successfully
```

Never mark a destructive migration done without explicit review.

---

## 11. Contract tests

FastAPI OpenAPI is canonical.

CI should:

1. generate OpenAPI;
2. generate TypeScript client;
3. compile frontend;
4. fail if generated artifacts drift.

---

## 12. External providers

Do not call live external providers in normal CI.

Use:

- adapter-level mocks/fakes;
- recorded fixtures where appropriate;
- separate scheduled/manual integration checks.

When AI arrives, maintain a dedicated evaluation suite separate from deterministic CI.

---

## 13. Quality thresholds

Do not use code coverage percentage as the primary definition of quality.

Coverage can identify blind spots, but acceptance criteria and invariant protection are more important.

---

## 14. Test naming

Names should communicate:

```text
given / action / expected outcome
```

Example:

`test_consume_slot_creates_immutable_nutrition_snapshot`

Avoid meaningless names such as `test_service_1`.

---

## 15. Required verification by task type

### Backend/domain-only

- format/lint;
- typecheck;
- unit tests;
- relevant integration tests.

### Database change

All above plus:

- migration fresh-db test;
- previous-schema upgrade test when relevant.

### API change

All above plus:

- API tests;
- OpenAPI/client regeneration.

### Frontend flow

- frontend lint/typecheck;
- component tests where useful;
- relevant Playwright E2E;
- mobile manual review.

### Nutrition data change

- nutrition seed validation;
- calculation regression tests;
- provenance/preparation-state checks.

---

## 16. Completion evidence

The implementing task file must state:

- exact checks run;
- pass/fail result;
- tests added/changed;
- anything intentionally not tested and why.

"Tests should pass" is not completion evidence.
