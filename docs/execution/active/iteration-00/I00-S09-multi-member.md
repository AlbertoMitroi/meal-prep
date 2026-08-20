# I00-S09 — Multi-Member Household

**Status:** BLOCKED  
**Iteration:** 0  
**Depends on:** I00-S06

## Goal

Expose the already-correct household model in V0 so at least two members can have independent plans, targets, logs and weight history.

## User outcome

A household can track two people independently and switch Today context without two separate apps.

## Read before implementation

- `AGENTS.md`
- `docs/product/01-USER-MODEL-AND-USE-CASES.md`
- `docs/product/06-DOMAIN-MODEL.md`
- `docs/product/08-UX-FLOWS.md`
- `docs/product/10-ITERATION-0-IMPLEMENTATION-BLUEPRINT.md`

## Scope

### Backend/domain

- Add member application service.
- Assign existing coach plan to a member.
- Support member-specific optional manual targets.
### API

- Household summary/member add.
- Assign plan/targets to member as needed.
### Frontend

- `Eu → Household` simple member list/add.
- Plan assignment prompt.
- Member switcher in Today.
- Remember last selected member as UI preference if helpful.

## Non-goals

- Invitations/accounts for second member.
- Shared personalized portions.
- Household optimization.
- Shared cooking.
- Complex roles/permissions UI.

## Domain / architecture rules

- Member can exist without Account.
- Logs/targets/weight remain member-owned.
- Household data must not be duplicated per member.
- Switching members preserves screen context.

## Acceptance criteria

- [ ] Second member can be created with only name.
- [ ] Coach plan can be assigned.
- [ ] Targets can differ.
- [ ] Today logs are independent.
- [ ] Weight histories are independent.
- [ ] Switching member is obvious and safe.

## Tests

- Member creation/plan assignment integration.
- Member A/B log isolation.
- Authorization.
- E2E create second member→switch→log independently.

## Required verification

- Use two real member fixtures and confirm Today totals/history never bleed between them.

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
