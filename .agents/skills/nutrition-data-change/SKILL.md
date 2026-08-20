---
name: nutrition-data-change
description: Safely add or modify foods, nutrients, recipes or coach-plan seed data with explicit provenance, preparation state and deterministic nutrition validation.
---

# Nutrition Data Change

This skill is mandatory for V0 food/recipe/coach-plan data changes.

## Non-negotiable rules

- Never invent a trainer-provided quantity.
- Unknown nutrient value is not zero.
- Raw/cooked/preparation state must be explicit where it changes meaning.
- Every nutrition datum requires provenance/source.
- Active V0 foods/recipes must contain the nutrients required by V0.
- Recipe nutrition is calculated from ingredients.
- Historical FoodLog snapshots are never changed by editing current catalog data.
- Seed data uses stable codes and is idempotent.

## Workflow

1. Locate the exact source information.
2. Normalize food identity and preparation state.
3. Add/update nutrient values with source.
4. Add/update recipe ingredient quantities.
5. Validate units.
6. Run seed validation.
7. Calculate every affected recipe nutrition.
8. Compare outputs for plausibility and source consistency.
9. Add/update regression fixtures where useful.
10. Record any unavailable data as unknown; do not fill it with guesses.

## Missing coach-plan quantity

If an active recipe requires a quantity that the source does not specify:

- do not activate the recipe;
- retrieve/clarify the original value;
- or explicitly exclude that recipe from V0.

Never choose a "reasonable" gram amount on behalf of the product.
