# I00-S02 — Auth & Household

**Status:** BLOCKED  
**Iteration:** 0  
**Depends on:** I00-S01

## Goal

Establish authenticated identity and the correct Account → Household → Member foundation.

## User outcome

A signed-in user has a persistent household/member identity that future nutrition data can safely belong to.

## Read before implementation

- `AGENTS.md`
- `docs/product/01-USER-MODEL-AND-USE-CASES.md`
- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`

## Scope

### Backend/domain

- Implement Account, Household, HouseholdMembership and Member persistence.
- Implement CurrentActor/JWT verification and household authorization helpers.
- Create a safe account bootstrap path after authentication.
### Database

- Alembic migration for identity/household tables and constraints.
### API

- `GET /v1/me` or equivalent authenticated bootstrap read model.
### Frontend

- Configure Supabase Auth session flow.
- Minimal login/sign-up/sign-out.
- Authenticated app shell and redirect behavior.

## Non-goals

- Member physiological profile.
- Nutrition targets.
- Multi-member management UI; added in S09.
- Direct frontend access to application tables.

## Domain / architecture rules

- Account and Member remain distinct.
- Authorization is never inferred from a client-provided member/household ID.
- FastAPI is the application authority even if Supabase provides Auth/Postgres.

## Acceptance criteria

- [ ] User can sign up/sign in/sign out.
- [ ] Authenticated request resolves one Account safely.
- [ ] Unrelated household/member IDs cannot be accessed.
- [ ] Bootstrap is idempotent.
- [ ] Session refresh does not create duplicate Accounts.

## Tests

- JWT/auth failure cases.
- Account bootstrap idempotency.
- Cross-household authorization integration tests.
- Frontend auth smoke/E2E path.

## Required verification

- Run migration clean + upgrade path.
- Run authorization tests.
- Verify no frontend code reads app tables directly.

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
