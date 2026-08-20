---
name: quality-gate
description: Review a completed task before it is marked done, checking scope, canonical docs, architecture boundaries, UX, migrations, tests and completion evidence.
---

# Quality Gate

Use before setting any meaningful task to DONE.

Prefer a second model/person for this review.

## 1. Requirement review

Read:

- task goal;
- user outcome;
- scope/non-goals;
- acceptance criteria;
- referenced canonical docs.

Confirm the implementation solves the task actually requested.

## 2. Scope review

Look for:

- future iteration features;
- unrelated refactors;
- placeholder modules/screens;
- new dependencies/infrastructure not required by the task.

Record unrelated debt instead of accepting scope creep.

## 3. Architecture review

Check:

- frontend does not own canonical calculations;
- frontend does not bypass FastAPI for app tables;
- routes are thin;
- engines are framework-free;
- module persistence ownership is respected;
- provider types do not leak into domain;
- source-of-truth vs derived state remains clear;
- historical snapshots are preserved where applicable.

## 4. Domain/data review

Check:

- Account/Household/Member semantics;
- Food/Recipe/Meal/FoodLog distinctions;
- planned vs actual;
- units/preparation state;
- unknown vs zero;
- authorization;
- transaction/idempotency needs;
- timezone/local-day semantics.

## 5. UX review

For user-facing work:

- one obvious primary action;
- common path short;
- mobile first;
- no unnecessary configuration;
- loading/empty/error states;
- advanced controls secondary;
- no shame/danger language for ordinary nutrition;
- no future disabled surfaces;
- accessibility/focus/labels.

## 6. Database review

If schema changed:

- Alembic migration exists;
- fresh DB upgrade passes;
- prior schema upgrade tested where meaningful;
- constraints/indexes sensible;
- no silent data-loss risk.

## 7. Contract review

If API changed:

- Pydantic/OpenAPI correct;
- generated TypeScript client current;
- frontend compiles against generated contract.

## 8. Test review

Verify required:

- unit;
- PostgreSQL integration;
- API;
- component;
- E2E;

according to task type.

Do not accept "not run" without an explicit valid reason.

## 9. Manual acceptance

Execute the user acceptance flow where practical.

For UI, inspect mobile layout.

## 10. Completion evidence

Task must record:

- what changed;
- files;
- migrations;
- checks/commands;
- results;
- known limitations/risks.

## Output

Report findings in priority order:

1. blockers;
2. correctness/security issues;
3. architecture/domain issues;
4. UX issues;
5. maintainability/debt;
6. optional polish.

If no blockers remain, explicitly state that the task satisfies its Definition of Done.
