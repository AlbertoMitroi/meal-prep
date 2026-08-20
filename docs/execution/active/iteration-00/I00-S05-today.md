# I00-S05 — Today

**Status:** BLOCKED  
**Iteration:** 0  
**Depends on:** I00-S04

## Goal

Build the primary V0 product surface: Today read model with current member, daily nutrition summary, four trainer-plan slots and recipe options.

## User outcome

Opening the app immediately shows what the user has to eat today and where they stand nutritionally.

## Read before implementation

- `AGENTS.md`
- `docs/product/08-UX-FLOWS.md`
- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/10-ITERATION-0-IMPLEMENTATION-BLUEPRINT.md`

## Scope

### Backend/domain

- Implement Today read/query service derived from active plan assignment + local date + FoodLogs (none initially) + active targets.
- Resolve household-local date correctly.
### API

- `GET /v1/today` with member/date support and authorization.
### Frontend

- Today page/header.
- Compact nutrition summary supporting no targets/partial targets/full targets.
- Four meal slot cards.
- Next pending meal highlight.
- Meal options bottom sheet.
- Recipe detail read view required before logging.

## Non-goals

- Recommendation engine.
- Persisted selected-but-not-eaten meal state.
- Weekly planner.
- Micronutrient insights.
- Skip/lock/prepared statuses.

## Domain / architecture rules

- Today is a read model, not a new source of truth.
- First pending slot is visually dominant.
- No dashboard overload.
- Do not invent statuses beyond V0 needs.

## Acceptance criteria

- [ ] Today renders with no FoodLogs.
- [ ] No-target and partial-target states look intentional.
- [ ] All four coach slots/options are visible.
- [ ] Recipe detail shows ingredient quantities + short nutrition.
- [ ] Member/date access is authorized.
- [ ] Local day uses household timezone.

## Tests

- Today query no-log fixture.
- No/partial/full target fixtures.
- Timezone boundary tests.
- API authorization tests.
- Frontend loading/empty/error behavior.

## Required verification

- Manual mobile usability: user can identify next action in seconds.
- Above-fold review for nutrition summary + next meal.

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
