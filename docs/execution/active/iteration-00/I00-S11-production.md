# I00-S11 — Production Hardening & Dogfood Release

**Status:** BLOCKED  
**Iteration:** 0  
**Depends on:** I00-S10

## Goal

Deploy Iteration 0 safely, verify operational basics and begin the real 7-day dogfood period.

## User outcome

The product is reliably available as the primary trainer-plan tracker and data is protected/persistent.

## Read before implementation

- `AGENTS.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`
- `docs/product/10-ITERATION-0-IMPLEMENTATION-BLUEPRINT.md`
- `docs/engineering/TESTING-STANDARDS.md`
- `docs/product-feedback/ITERATION-00-DOGFOOD.md`

## Scope

### Infrastructure

- Production Next.js deployment.
- Production FastAPI container/service.
- Managed PostgreSQL/Auth configuration.
- HTTPS/CORS/env/secrets.
- Automated backups/PITR as appropriate.
### Quality

- Error monitoring.
- Structured logging/request IDs.
- Health/readiness checks.
- Security/authorization review.
- Migration/seed production procedure.
- Full CI and E2E verification.
### Release

- Create dogfood baseline.
- Start 7-day feedback log.
- Fix only correctness bugs/UX blockers inside Iteration 0 scope.

## Non-goals

- Iteration 1 features.
- Redis/worker/AI infrastructure.
- Premature scaling work.

## Domain / architecture rules

- No release if nutrition totals, authorization, timezone day boundaries or persistence are unreliable.
- Backups must exist before meaningful real data is trusted.
- Production secrets never enter repository.

## Acceptance criteria

- [ ] Production deploy succeeds.
- [ ] Fresh production onboarding works.
- [ ] Critical E2E/smoke flows pass.
- [ ] Error monitoring receives test event.
- [ ] Backup/recovery policy is confirmed.
- [ ] Two-member real usage is possible.
- [ ] Dogfood document is started.
- [ ] Iteration 0 release blockers from Blueprint are cleared.

## Tests

- Full CI.
- Production smoke tests.
- Authorization/security regression suite.
- Migration + seed verification.
- Critical Playwright flows against release environment where safe.

## Required verification

- Record deployment URLs/config ownership privately where appropriate.
- Start 7-day dogfood and update CURRENT/INDEX status.

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
