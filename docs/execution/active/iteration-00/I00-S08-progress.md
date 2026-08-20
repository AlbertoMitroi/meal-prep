# I00-S08 — Progress / Weight

**Status:** BLOCKED  
**Iteration:** 0  
**Depends on:** I00-S02

## Goal

Add simple manual weight tracking and a calm progress view without interpretation or adaptive logic.

## User outcome

The user can add today's weight in seconds and see a simple history/trend.

## Read before implementation

- `AGENTS.md`
- `docs/product/04-NUTRITION-SPEC.md`
- `docs/product/08-UX-FLOWS.md`
- `docs/product/10-ITERATION-0-IMPLEMENTATION-BLUEPRINT.md`

## Scope

### Backend/domain

- Implement WeightLog with source/confidence and member ownership.
- Provide weight history query and simple display trend calculation if specified for V0.
### Database

- Migration/indexes for WeightLog.
### API

- Create weight log.
- Read member weight history/range.
### Frontend

- `+ → Greutate` quick entry.
- `Eu → Progres` current weight, history and one simple chart.

## Non-goals

- Scale photo.
- Body-fat metrics.
- Automatic interpretation.
- Target recalibration.
- Complex progress analytics.

## Domain / architecture rules

- Weight entry is observed data, not a trigger for automatic nutrition changes.
- Do not react to one measurement.
- Personal member data must respect authorization/visibility boundaries.

## Acceptance criteria

- [ ] Weight can be saved quickly.
- [ ] Refresh persists data.
- [ ] History is chronological/correct.
- [ ] Progress view works with 0/1/many entries.
- [ ] No medical/goal interpretation is shown.

## Tests

- Weight create/read integration.
- Authorization.
- Empty/one/multiple record frontend states.
- Timezone/date display.

## Required verification

- Manual add from global + and verify value appears in Progress immediately.

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
