# I00-S03 — Catalog & Nutrition

**Status:** BLOCKED  
**Iteration:** 0  
**Depends on:** I00-S01

## Goal

Create the curated V0 food/recipe catalog, deterministic nutrition calculation engine and versioned coach-plan template seed.

## User outcome

The system can represent the trainer plan correctly and calculate calories/macros from exact ingredient quantities.

## Read before implementation

- `AGENTS.md`
- `docs/product/03-CORE-ENGINES.md`
- `docs/product/04-NUTRITION-SPEC.md`
- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/10-ITERATION-0-IMPLEMENTATION-BLUEPRINT.md`

## Scope

### Backend/domain

- Implement Nutrient, Food, FoodNutrientValue, Recipe, RecipeIngredient.
- Implement MealPlanTemplate, template slots/options in the simplest V0-compatible owner.
- Implement framework-free NutritionVector and calculation functions.
- Implement seed loader and validation.
### Database

- Alembic migration for catalog/nutrition/template tables.
### Data

- Create nutrient seed for energy/protein/carbohydrate/fat and optional fiber.
- Create only foods needed by the current coach plan plus explicitly approved manual-log basics.
- Create versioned `coach_plan_v1` from the exact trainer data.
### API

- Provide only the minimal read/query support needed by following slices; no public generic admin CRUD.

## Non-goals

- Open Food Facts.
- Barcode/product/brand/package models.
- Automatic nutrition targets.
- Micronutrient UI.
- Generic combinatorial meal builder.
- Inventing missing trainer quantities.

## Domain / architecture rules

- Unknown nutrient data is not zero.
- Raw/cooked/preparation state is explicit where relevant.
- All seed nutrition has provenance/source.
- Recipe totals are calculated from ingredients, not trusted from hand-entered totals.
- Incomplete active recipes are rejected.

## Acceptance criteria

- [ ] Every active V0 recipe calculates deterministic kcal/protein/carbs/fat.
- [ ] Coach plan has four correct slots and only trainer-approved variants.
- [ ] Seed is idempotent.
- [ ] No active recipe contains invented quantities.
- [ ] Nutrition engine has no DB/FastAPI imports.

## Tests

- Per-100g scaling.
- Recipe summation.
- 50/75/100/125% scaling.
- Missing required nutrition rejection.
- Seed reference/duplicate/idempotency validation.
- Preparation-state fixtures.

## Required verification

- Run seed validation.
- Print/inspect calculated nutrition for every active recipe.
- Compare coach-plan seed against source plan before task completion.

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
