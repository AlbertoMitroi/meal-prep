# I00-S10 — UX Polish & PWA

**Status:** BLOCKED  
**Iteration:** 0  
**Depends on:** I00-S07, I00-S08, I00-S09

## Goal

Make Iteration 0 feel like a complete minimalist product on mobile rather than a collection of working screens.

## User outcome

The app is intuitive without explanation, fast for common actions, installable and calm across success/loading/empty/error states.

## Read before implementation

- `AGENTS.md`
- `docs/product/08-UX-FLOWS.md`
- `docs/product/10-ITERATION-0-IMPLEMENTATION-BLUEPRINT.md`
- `docs/engineering/ENGINEERING-STANDARDS.md`

## Scope

### Frontend

- Finalize `Azi / + / Eu` navigation.
- Responsive/mobile-first layout.
- Loading skeletons.
- Empty/error states with next actions.
- Undo/toast behavior.
- Accessibility/focus/labels.
- PWA manifest/icons/metadata.
- Consistent copy and visual hierarchy.
- Desktop graceful centering/expansion without dashboard redesign.
### Backend/API

- Only changes required to support clean UX/error contracts; no new product capability.

## Non-goals

- New features.
- Fancy animation system.
- Offline writes.
- Desktop analytics dashboard.
- Future navigation tabs.

## Domain / architecture rules

- One primary action.
- No disabled future features.
- No modal stacking.
- Ordinary nutrition deviation is not danger/red.
- Common meal logging stays short.
- Advanced adjustment stays secondary.

## Acceptance criteria

- [ ] Critical flows work comfortably at 375–430 px.
- [ ] No dead-end empty/error state.
- [ ] Keyboard/focus/semantic controls are usable.
- [ ] App is installable as PWA.
- [ ] No future feature appears in navigation/UI.
- [ ] A non-developer can complete core acceptance tasks without coaching.

## Tests

- Relevant component/accessibility tests.
- Full Iteration 0 Playwright suite.
- Manual mobile usability checklist.

## Required verification

- Run usability tasks from UX spec without explanation.
- Review normal/slow/error network states.
- Review one-member and two-member states.

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
