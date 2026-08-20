# I00-S06 — Meal Logging

**Status:** BLOCKED  
**Iteration:** 0  
**Depends on:** I00-S05

## Goal

Allow a member to consume one allowed trainer-plan option, store actual nutrition correctly and immediately update Today.

## User outcome

The user taps `Am mâncat`; the slot becomes complete and the day totals update without extra confirmation.

## Read before implementation

- `AGENTS.md`
- `docs/product/04-NUTRITION-SPEC.md`
- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/08-UX-FLOWS.md`
- `docs/product/09-TECHNICAL-ARCHITECTURE.md`
- `docs/product/10-ITERATION-0-IMPLEMENTATION-BLUEPRINT.md`

## Scope

### Backend/domain

- Implement FoodLog, FoodLogItem and NutritionSnapshot.
- Implement consume-slot application service and undo/correction-safe delete path.
- Calculate snapshot through Nutrition Engine.
### Database

- Migration for logs/items/snapshots and appropriate indexes/constraints.
### API

- Consume slot endpoint.
- Undo/delete log endpoint.
- Return updated Today data/fragment.
### Frontend

- `Am mâncat` primary action.
- Consumed meal state.
- Today totals refresh.
- Undo snackbar.

## Non-goals

- Portion adjustments (S07).
- Recommendation/learning.
- Explicit skipped state.
- Meal planner entities beyond V0 template linkage.

## Domain / architecture rules

- Historical consumed nutrition uses immutable snapshot.
- Actual quantities live in FoodLogItem; canonical recipe remains unchanged.
- Only recipes allowed by the active template slot may complete that slot.
- Prevent duplicate active slot log for member/local-date/slot.
- No confirmation modal for ordinary logging; use undo.

## Acceptance criteria

- [ ] Allowed meal logs successfully.
- [ ] Slot becomes consumed.
- [ ] Totals are correct and persist after refresh.
- [ ] Duplicate consumption is prevented.
- [ ] Undo restores pending state and totals.
- [ ] Manual unrelated log cannot falsely complete a plan slot.
- [ ] Cross-household mutation is rejected.

## Tests

- Consume application integration test.
- Nutrition snapshot regression test.
- Duplicate slot case.
- Undo case.
- Timezone/local-day case.
- E2E log→refresh→undo.

## Required verification

- Inspect stored snapshot/items for one real coach recipe.
- Manual phone flow must feel effectively one action after recipe selection.

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
