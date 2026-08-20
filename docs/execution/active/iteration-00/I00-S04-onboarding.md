# I00-S04 — Onboarding

**Status:** BLOCKED  
**Iteration:** 0  
**Depends on:** I00-S02, I00-S03

## Goal

Let a new authenticated user reach Today through a minimal onboarding flow that creates the required household/member/plan assignment and optional manual targets.

## User outcome

A new user can set a name, choose/use the trainer plan, optionally enter targets and reach the product in under a minute.

## Read before implementation

- `AGENTS.md`
- `docs/product/08-UX-FLOWS.md`
- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`
- `docs/product/10-ITERATION-0-IMPLEMENTATION-BLUEPRINT.md`

## Scope

### Backend/domain

- Implement NutritionTargetProfile/NutrientTarget V0 manual mode.
- Implement MemberPlanAssignment.
- Create one transactional onboarding application service.
### Database

- Migration for target profiles/targets/plan assignments.
### API

- `POST /v1/onboarding` domain-oriented endpoint.
### Frontend

- Minimal onboarding steps: member name → plan → optional targets → Today.
- Skip targets path.
- Resume/avoid duplicate onboarding safely.

## Non-goals

- Age/height/sex/activity/goal collection.
- Automatic TDEE/target estimation.
- Budget, schedule, preferences, meal prep setup.
- Tutorial carousel.

## Domain / architecture rules

- Targets are optional and may be partial.
- Target updates/history use effective dating; do not mutate history casually.
- Household naming/configuration is not required onboarding friction.
- No global setup wizard.

## Acceptance criteria

- [ ] Fresh authenticated account completes onboarding without manual DB work.
- [ ] Skipping targets still produces a usable account.
- [ ] Partial targets are valid.
- [ ] Re-submission/retry does not duplicate household/member/assignment.
- [ ] Successful onboarding lands on Today.

## Tests

- Transactional onboarding integration test.
- No-target and partial-target cases.
- Retry/idempotency case.
- Authorization/authentication failures.
- E2E new-user onboarding.

## Required verification

- Mobile review at 375–430 px.
- Measure number of steps/actions; remove non-essential questions.

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
