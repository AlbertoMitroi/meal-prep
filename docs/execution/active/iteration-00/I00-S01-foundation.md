# I00-S01 — Foundation

**Status:** READY  
**Iteration:** 0  
**Depends on:** —

## Goal

Create a clean, runnable monorepo foundation for Next.js + FastAPI + PostgreSQL with migrations, generated API contracts and CI.

## User outcome

There is no user-facing product value yet; this slice exists to make every following slice safe and fast to deliver.

## Read before implementation

- `AGENTS.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`
- `docs/engineering/ENGINEERING-STANDARDS.md`
- `docs/engineering/TESTING-STANDARDS.md`

## Scope

### Backend/domain

- Create FastAPI app and `/health/live`, `/health/ready`.
- Configure Pydantic settings, structured errors/logging skeleton.
- Configure SQLAlchemy 2 connection/session infrastructure.
### Database

- Local PostgreSQL development setup.
- Configure Alembic with initial empty migration baseline.
### API

- Expose OpenAPI schema.
- Add reproducible command to generate TypeScript API client.
### Frontend

- Create Next.js/React/TypeScript app.
- Configure Tailwind/shadcn base and TanStack Query provider.
- Connect a trivial typed API health call for development verification.
### Tooling

- Monorepo/workspace setup.
- Python: uv, Ruff, Pyright, pytest.
- Frontend lint/typecheck/test/build commands.
- CI pipeline skeleton.

## Non-goals

- Authentication.
- Product/domain tables.
- Coach-plan data.
- Redis, workers, AI, OR-Tools or future modules.

## Domain / architecture rules

- One monorepo; one FastAPI service; one PostgreSQL database.
- Do not create empty future product modules.
- Generated API client must be reproducible, not hand-maintained.

## Acceptance criteria

- [ ] `make/just dev` or documented equivalent starts required local services.
- [ ] Web app loads.
- [ ] FastAPI health endpoints work.
- [ ] PostgreSQL connectivity is verified.
- [ ] `alembic upgrade head` works on an empty DB.
- [ ] OpenAPI → TypeScript generation works.
- [ ] CI executes lint/typecheck/tests/build without product-code placeholders.

## Tests

- Backend health/API smoke test.
- Migration smoke test against PostgreSQL.
- Frontend build/typecheck.
- API client generation drift check.

## Required verification

- Run backend lint/typecheck/tests.
- Run frontend lint/typecheck/build/tests.
- Run migration on clean PostgreSQL.
- Generate API client and verify clean diff.

Use `.agents/skills/quality-gate/SKILL.md` before requesting completion.

## Completion evidence

### Summary

_Not filled yet._

### Files changed

_Not filled yet._

### Migrations

_Not filled yet._

### Commands/checks executed

_Not filled yet._

### Manual acceptance verification

_Not filled yet._

### Known limitations / risks

_Not filled yet._

### Reviewer notes

_Not filled yet._
