---
name: implement-vertical-slice
description: Implement a scoped task from docs/execution as a complete vertical slice while preserving product, architecture, UX and test requirements.
---

# Implement Vertical Slice

Use this skill for non-trivial execution tasks.

## Workflow

1. Read `AGENTS.md`.
2. Read `docs/execution/CURRENT.md`.
3. Read the complete active task.
4. Read only the product/engineering/ADR files referenced by the task.
5. Inspect existing affected code before designing new abstractions.
6. Confirm dependencies and task status.
7. Identify the smallest complete vertical change.
8. Implement canonical domain/backend logic first when truth/calculation is involved.
9. Add migration/persistence changes if required.
10. Update API schemas and regenerate the TypeScript client.
11. Implement the user-facing flow.
12. Add loading/empty/error behavior.
13. Add the required tests.
14. Run `quality-gate`.
15. Fill task completion evidence.

## Scope control

Do not:

- implement future iteration capabilities;
- create placeholder modules/screens;
- perform unrelated cleanup;
- introduce infrastructure without current need.

Record unrelated debt in `docs/execution/TECH-DEBT.md`.

## Stop conditions

Stop and request/record a decision when:

- task conflicts materially with canonical product docs;
- required domain behavior is ambiguous and changes user-visible semantics;
- implementing cleanly requires a new architectural decision;
- required source data is missing and would have to be invented.
