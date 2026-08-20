# Development Workflow

## Purpose

This document defines how humans, Claude and Codex execute work in the repository.

The workflow is intentionally simple and task-centric.

---

## 1. Find the active work

Always begin with:

`docs/execution/CURRENT.md`

It points to:

- active iteration;
- active execution index;
- current product blueprint.

Do not choose future work because it looks interesting.

---

## 2. Select one task

Read the task file completely.

Confirm:

- status is `READY`;
- dependencies are complete;
- scope is clear;
- acceptance criteria are testable.

If a dependency is not complete, do not bypass it silently.

---

## 3. Read only relevant canonical context

Read:

1. `AGENTS.md`;
2. the task;
3. product docs referenced by the task;
4. relevant engineering standards;
5. relevant ADRs;
6. existing code in the affected modules.

Do not load every document blindly when the task is narrow.

---

## 4. Work isolation

For non-trivial work, use a dedicated branch/worktree.

Example:

```bash
git switch -c feat/i00-s06-meal-logging
```

or an isolated worktree.

Never let Claude and Codex modify the same working tree at the same time.

---

## 5. Plan the smallest vertical change

Before coding, identify:

- domain/backend changes;
- persistence/migration changes;
- API contract changes;
- frontend changes;
- tests;
- acceptance verification.

Do not build future iteration behavior.

---

## 6. Implement canonical truth first

When a feature affects canonical state/calculation:

1. domain/engine;
2. application service;
3. persistence;
4. API;
5. generated client;
6. frontend.

UI-only polish tasks may naturally start in the frontend.

---

## 7. Keep the build runnable

Prefer small coherent checkpoints.

Do not leave the repository broken for long stretches while implementing speculative layers.

---

## 8. Update contracts immediately

If Pydantic/FastAPI schemas change:

1. regenerate OpenAPI;
2. regenerate TypeScript client;
3. fix frontend compilation.

Do not postpone contract drift until task end.

---

## 9. Migrations

For schema changes:

1. inspect current schema/migrations;
2. implement SQLAlchemy model changes;
3. create/review Alembic migration;
4. test on clean PostgreSQL;
5. test upgrade path when meaningful;
6. document data migration concerns.

---

## 10. Verification loop

Run the narrowest relevant checks while developing.

Before completion, run the full task-required quality gate.

Use `.agents/skills/quality-gate/SKILL.md`.

---

## 11. Manual acceptance

Automated tests do not replace UX verification.

For user-facing tasks:

- open the app at mobile width;
- execute the acceptance flow without developer-only shortcuts;
- verify loading/empty/error states where relevant.

---

## 12. Update task completion evidence

Before requesting review, fill the task's completion section:

- summary;
- files changed;
- migrations;
- commands/checks;
- results;
- manual verification;
- limitations/risks.

Do not change task status to `DONE` yourself if repository policy assigns final status to reviewer/maintainer.

---

## 13. Second-agent review

Preferred for meaningful slices.

Reviewer should:

1. read the same task;
2. inspect canonical docs referenced by it;
3. review the diff;
4. run/inspect quality gate evidence;
5. focus on correctness, scope, architecture and UX;
6. report findings by severity.

The reviewer must not expand scope with personal redesign preferences.

---

## 14. Technical debt

If unrelated debt is discovered:

record it in `docs/execution/TECH-DEBT.md`.

Do not use the active task as an excuse to fix the entire area.

---

## 15. ADR trigger

Create an ADR before/with implementation when introducing a meaningful architectural decision, such as:

- new infrastructure service;
- new persistence strategy;
- significant module boundary change;
- replacing a core framework/provider strategy;
- intentional departure from `09-TECHNICAL-ARCHITECTURE.md`.

Small implementation choices do not need ADRs.

---

## 16. Product-spec conflict

If code/task requirements conflict materially with `docs/product/`:

stop.

Do not silently choose one interpretation.

The canonical product document or task must be updated explicitly.

---

## 17. Task completion

A task becomes complete only when:

- acceptance criteria are satisfied;
- quality checks pass;
- relevant docs are updated;
- task evidence exists;
- review blockers are resolved.

Then update the iteration `INDEX.md`.

---

## 18. Parallel work

Parallelize only independent tasks.

Do not parallelize simply because two agents are available.

Use the dependency graph in the iteration index.

---

## 19. Release progression

Iteration 0 must reach real dogfood usage before Iteration 1 implementation begins.

Dogfood feedback belongs in:

`docs/product-feedback/ITERATION-00-DOGFOOD.md`

Only bugs, UX blockers and correctness issues should expand Iteration 0 during dogfooding.
