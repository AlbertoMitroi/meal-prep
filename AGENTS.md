## Mission

Build the product defined in `docs/product/`.

The current implementation target is defined in:
`docs/execution/CURRENT.md`.

Do not invent product behavior when a canonical specification exists.

---

## Sources of Truth

### Product
`docs/product/`

### Current execution
`docs/execution/CURRENT.md`
and the referenced active iteration/task.

### Engineering standards
`docs/engineering/`

### Architectural decisions
`docs/adr/`

---

## Precedence

When instructions conflict:

1. Explicit user/task instruction
2. Current iteration blueprint
3. Product invariants/specifications
4. Technical architecture
5. Engineering standards
6. Existing implementation patterns

Never silently resolve a meaningful conflict by inventing behavior.

---

## Mandatory Reading

Before implementing a task:

1. Read its task file.
2. Read the product documents explicitly referenced by it.
3. Read relevant engineering standards.
4. Inspect the existing implementation before creating new abstractions.

Do not read all documentation blindly unless the task requires it.

---

## Current Product Rule

Only implement capabilities belonging to the active iteration.

Do not pre-build future features.

No placeholder future tabs.
No empty future modules.
No speculative infrastructure.

---

## Architecture Guardrails

- Frontend never calculates canonical nutrition.
- Frontend never accesses application tables directly.
- FastAPI owns application/domain operations.
- Core engines never query the database.
- AI never writes directly to persistence.
- Historical nutrition snapshots are immutable.
- Household and Member are distinct concepts.
- Food and Product are distinct concepts.
- Recipe, Meal and FoodLog are distinct concepts.
- Planned and actual data remain distinct.
- External provider data must be normalized before domain use.
- Derived state must be rebuildable.
- Do not introduce infrastructure until a shipped capability requires it.

---

## UX Guardrails

- Mobile-first.
- One obvious primary action.
- Complexity behind progressive disclosure.
- Never require information already known by the system.
- Never expose backend/module terminology to users.
- No disabled "coming soon" product surfaces.
- Simple does not mean removing advanced capability; advanced controls are secondary.
- Common paths must remain extremely short.
- Error states must provide a next action.

---

## Implementation Workflow

For non-trivial tasks:

1. Understand task and relevant specifications.
2. Inspect existing code.
3. Identify the smallest vertical implementation.
4. Implement backend/domain first where canonical truth is involved.
5. Update API contracts.
6. Implement UI.
7. Add/update tests.
8. Run required quality checks.
9. Verify acceptance criteria.
10. Update the task file with completion evidence.

---

## Change Discipline

Before introducing:
- a new dependency,
- a new architectural pattern,
- a new database concept,
- a new infrastructure service,

prove that existing architecture cannot solve the requirement cleanly.

Architectural deviations require an ADR.

---

## Database

- Alembic only for schema migrations.
- No production `create_all`.
- Preserve historical data.
- Prefer additive migrations.
- Money uses Decimal/NUMERIC.
- Times use timezone-aware timestamps.
- Nutrition quantities preserve units and preparation state.
- Missing nutrient data is NULL, never zero.

---

## API

- Domain-oriented actions, not raw table CRUD.
- Pydantic/FastAPI is canonical API schema.
- TypeScript API client is generated from OpenAPI.
- Never manually duplicate backend contracts.
- API errors use the standard error contract.

---

## Verification

A task is not done until:
- formatting passes,
- lint passes,
- type checking passes,
- relevant unit tests pass,
- integration tests pass where required,
- relevant E2E tests pass,
- generated API client is current,
- acceptance criteria are manually verified.

---

## Completion Report

Report:
- what changed,
- important design decisions,
- tests/checks executed,
- migrations added,
- known limitations,
- remaining risks.

Do not call a task complete if required checks are failing.