# Contributing

This repository is developed by humans and coding agents under the same rules.

## Canonical context

Before changing product behavior, read:

- `AGENTS.md`
- `docs/execution/CURRENT.md`
- the active task file
- the product/engineering documents referenced by that task

`docs/product/` is the canonical product specification. Do not duplicate it in implementation notes.

## Branches

Use one branch/worktree per non-trivial task.

Suggested naming:

- `feat/i00-s03-catalog-nutrition`
- `fix/i00-s06-duplicate-food-log`
- `chore/tooling-openapi-client`

Do not let two agents edit the same working tree concurrently.

## Scope

Implement only the active task.

If you discover unrelated work:

1. do not refactor it casually;
2. record it in `docs/execution/TECH-DEBT.md`;
3. continue the current task.

Future iteration features must not be pre-built unless the current task explicitly requires an enabling change.

## Product and architecture rules

The repository rules in `AGENTS.md` are mandatory.

Particularly:

- canonical nutrition is calculated by the Python backend;
- the frontend never accesses application tables directly;
- engines do not query the database;
- AI does not write directly to persistence;
- historical nutrition snapshots are preserved;
- Account, Household and Member remain distinct concepts;
- Food, Product, Recipe, Meal and FoodLog are not interchangeable concepts;
- planned and actual data remain separate.

## Dependencies

Before adding a runtime dependency:

1. prove the current stack cannot solve the requirement cleanly;
2. prefer an existing dependency when appropriate;
3. document meaningful architectural consequences in an ADR.

Do not add infrastructure for hypothetical future scale.

## Database changes

All schema changes use Alembic.

A database change is not complete until:

- the migration upgrades a fresh PostgreSQL database;
- the migration upgrades the previous schema;
- constraints/indexes are reviewed;
- data-loss risk is understood;
- relevant integration tests pass.

Never use production `create_all()` as a migration strategy.

## Generated API client

FastAPI/Pydantic is the canonical API contract.

When an API schema changes:

1. regenerate OpenAPI;
2. regenerate the TypeScript client;
3. commit the generated changes;
4. ensure the frontend compiles against the generated contract.

Do not manually recreate backend DTOs in TypeScript.

## Tests and verification

Run the checks required by the task and `docs/engineering/TESTING-STANDARDS.md`.

A task is not complete because the code "looks right".

Completion requires evidence in the task file covering:

- files changed;
- migrations;
- commands executed;
- test results;
- manual acceptance checks;
- known limitations/risks.

## Review

For meaningful tasks, prefer a second model/person for review.

Recommended pattern:

- Claude implements → Codex reviews with `quality-gate`;
- Codex implements → Claude reviews with `quality-gate`.

The reviewer should review the task requirements and canonical docs, not only the diff.

## Commits

Keep commits scoped and understandable.

Avoid mixing:

- feature work;
- unrelated refactors;
- formatting of unrelated files;
- future-iteration scaffolding.

## Definition of done

A feature must be complete vertically where applicable:

- domain/backend;
- API contract;
- frontend;
- loading/empty/error behavior;
- tests;
- accessibility considerations;
- verification.

Do not ship disabled "coming soon" product surfaces.
