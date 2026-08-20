---
name: database-migration
description: Create and verify safe PostgreSQL/Alembic schema changes while preserving data, domain invariants and production upgrade paths.
---

# Database Migration

## Workflow

1. Read the task/domain model.
2. Inspect current SQLAlchemy models and migration history.
3. Identify schema change and data/backfill implications.
4. Prefer additive changes.
5. Implement model changes.
6. Generate/write Alembic migration.
7. Review generated SQL manually.
8. Add constraints/indexes intentionally.
9. Test `alembic upgrade head` from an empty PostgreSQL database.
10. Test upgrade from the previous schema/data fixture when meaningful.
11. Run relevant integration/API tests.
12. Document migration in task completion evidence.

## Rules

- PostgreSQL, not SQLite.
- No production `create_all()`.
- Money: NUMERIC/Decimal.
- Moments: timezone-aware timestamps.
- Local-day concepts: semantic date/timezone handling.
- Unknown nutrition: NULL, not zero.
- Avoid destructive changes unless explicitly approved.
- Historical nutrition/log data must not be silently rewritten.
- No core business logic in triggers.
- Do not use JSONB to avoid modeling queryable core domain entities.

## Destructive change checklist

If dropping/renaming/changing semantics:

- who reads/writes old field?
- is expand/migrate/contract safer?
- is backfill required?
- can old app version coexist during deploy?
- rollback/recovery plan?
