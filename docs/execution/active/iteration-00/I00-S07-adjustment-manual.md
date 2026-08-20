# I00-S07 — Adjustment & Manual Add

**Status:** BLOCKED  
**Iteration:** 0  
**Depends on:** I00-S06

## Goal

Add the V0 escape hatches: log a different percentage/ingredient quantity and manually add a seeded food or recipe.

## User outcome

The user can honestly track eating less/more than planned or an extra item without changing the canonical trainer recipe.

## Read before implementation

- `AGENTS.md`
- `docs/product/04-NUTRITION-SPEC.md`
- `docs/product/08-UX-FLOWS.md`
- `docs/product/10-ITERATION-0-IMPLEMENTATION-BLUEPRINT.md`

## Scope

### Backend/domain

- Support scale percentage and precise ingredient adjustments on consume.
- Support manual FoodLog from seeded Food or Recipe.
- Validate quantities/units/preparation states.
### API

- Extend consume contract or add explicit adjustment command.
- `POST /v1/food-logs` for minimal manual log flow.
### Frontend

- Adjustment bottom sheet with 50/75/100/125%.
- Advanced exact ingredient quantities.
- Universal `+` sheet: Masă / Greutate.
- Minimal search over local seeded foods/recipes.
- Manual quantity entry.

## Non-goals

- Custom food creation.
- Barcode/internet search.
- Custom recipe editor.
- Meal-photo estimation.

## Domain / architecture rules

- Adjustment modifies FoodLogItems only, never Recipe template.
- Manual extra logs have no plan slot and never complete one.
- Do not require gram precision when percentage is enough.
- Search remains local to seeded data.

## Acceptance criteria

- [ ] 75% log produces exactly scaled nutrition.
- [ ] Exact ingredient override produces recalculated snapshot.
- [ ] Extra banana/recipe can be logged without slot completion.
- [ ] Invalid/negative quantities are rejected.
- [ ] Common adjustment flow remains simple on mobile.

## Tests

- Scale math regression tests.
- Ingredient override calculation.
- Manual food vs recipe log integration.
- Extra log does not complete slot.
- Frontend adjustment/manual-add flow.

## Required verification

- Manually log 75%, 125% and one ingredient override; inspect totals and persisted items.

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
