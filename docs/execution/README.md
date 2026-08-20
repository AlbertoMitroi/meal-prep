# Execution Documentation

`docs/execution/` contains the mutable layer describing what is being built now.

It is intentionally separate from `docs/product/`, which defines canonical product truth.

## Structure

```text
CURRENT.md
TECH-DEBT.md
TASK-TEMPLATE.md

active/
  iteration-00/
    INDEX.md
    task files...

completed/
```

## Status vocabulary

Task status:

- `BLOCKED`
- `READY`
- `IN_PROGRESS`
- `IN_REVIEW`
- `DONE`

Only `READY` tasks whose dependencies are complete should be started.

## Rule

Execution documents may become stale quickly, so update them as part of the work.

Product documents should change only when the underlying product decision changes.
