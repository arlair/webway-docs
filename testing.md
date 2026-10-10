# Testing and Validation

Use for choosing verification scope. The target project's nearest `AGENTS.md` and `package.json`
define the exact commands.

## Test selection

- Pure logic: test deterministic transformations, calculations, validators and schema boundaries.
- Browser smoke: test critical pages, accessibility-visible outcomes and the most important user path.
- Integration: use real or realistic dependencies where the boundary is the behaviour under test; avoid large mock pyramids.
- UI implementation details and simple pass-through components usually do not need unit tests.

## Change-to-test guidance

- Bug fix → add or update a regression test when practical.
- New critical page or flow → add a high-level smoke test.
- Complex domain logic → add focused unit tests.
- Styling-only change → no test by default unless visual regression is part of the project workflow.

Prefer behaviour and accessibility assertions over class names or private component structure.

## Commands

Use scripts declared by the target project:

```bash
pnpm check
pnpm test
pnpm build:prod
```

These are examples, not universal scripts. From the workspace root, use the filter form, for
example `pnpm --filter travelwebway test`. Run only the checks needed by the change; avoid broad
workspace builds/tests when a project-level check is sufficient.
