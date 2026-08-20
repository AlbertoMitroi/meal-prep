# Iteration 0 — Coach Plan Tracker

Canonical scope is defined by the Iteration 0 Implementation Blueprint in `docs/product/`.

This execution plan turns that blueprint into individually buildable vertical slices.

## Dependency graph

```text
I00-S01 Foundation
   ├── I00-S02 Auth & Household
   └── I00-S03 Catalog & Nutrition
              │
I00-S02 ──────┴──→ I00-S04 Onboarding
                      ↓
                  I00-S05 Today
                      ↓
                  I00-S06 Meal Logging
                   ├────┬────┐
                   ↓    ↓    ↓
                 S07   S08   S09
                   └────┴────┘
                      ↓
                  I00-S10 UX Polish
                      ↓
                  I00-S11 Production & Dogfood Release
```

S08 may begin after identity/household foundations are stable, but final integration is validated after S06.

## Tasks

| Task | Title | Status | Depends on |
|---|---|---|---|
| I00-S01 | Foundation | READY | — |
| I00-S02 | Auth & Household | BLOCKED | S01 |
| I00-S03 | Catalog & Nutrition | BLOCKED | S01 |
| I00-S04 | Onboarding | BLOCKED | S02, S03 |
| I00-S05 | Today | BLOCKED | S04 |
| I00-S06 | Meal Logging | BLOCKED | S05 |
| I00-S07 | Adjustment & Manual Add | BLOCKED | S06 |
| I00-S08 | Progress / Weight | BLOCKED | S02 |
| I00-S09 | Multi-Member Household | BLOCKED | S06 |
| I00-S10 | UX Polish & PWA | BLOCKED | S07, S08, S09 |
| I00-S11 | Production Hardening & Dogfood Release | BLOCKED | S10 |

## Status rules

- Set a task to `IN_PROGRESS` when work actually starts.
- Set it to `IN_REVIEW` only after completion evidence is filled.
- Set it to `DONE` after review/acceptance.
- Update dependent tasks from `BLOCKED` to `READY` when all dependencies are `DONE`.

## Iteration exit

Do not open Iteration 1 implementation until:

- S01–S11 are DONE;
- Iteration 0 Definition of Done passes;
- 7-day real dogfood begins/completes according to the Blueprint;
- correctness/UX blockers discovered during dogfood are resolved or explicitly accepted.
