---
name: implement-frontend-flow
description: Implement or review a user-facing flow using the repository UX constitution, mobile-first hierarchy, progressive disclosure and canonical backend state.
---

# Implement Frontend Flow

## Read first

- active task;
- `docs/product/08-UX-FLOWS.md`;
- relevant API contract;
- existing feature patterns.

## Rules

- Mobile first.
- One clear primary action.
- Progressive disclosure for advanced details.
- Never expose backend/engine jargon.
- Never ask for information the system already knows.
- Do not calculate canonical nutrition/planning in TypeScript.
- Server state uses the API/TanStack Query.
- No disabled future product surfaces.
- Estimated information must look estimated.
- Ordinary nutrition deviation is not a danger state.
- Provide loading, empty and error states with a next action.
- Avoid modal-on-modal interaction.
- Preserve user input when navigating/backing out where reasonable.

## Implementation sequence

1. Define the user outcome and common path.
2. Verify the backend read/action contract is sufficient.
3. Build the simplest screen state first.
4. Add secondary/advanced controls.
5. Add loading/empty/error states.
6. Verify mobile layout.
7. Verify accessibility/focus/labels.
8. Add component/E2E tests required by the task.

## Review questions

- Can a first-time user discover the primary action without explanation?
- Is the common path the shortest path?
- Did we surface any complexity that should remain behind the system?
- Are advanced controls still available?
- Did we add a new tab/page because backend modules exist rather than because the user expects it?
