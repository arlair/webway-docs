# Coding Guidelines

Shared baseline for code changes. Use the nearest project configuration and formatter when they
are more specific.

## TypeScript

- Preserve a project's strict TypeScript settings; do not weaken them to make a change compile.
- Do not introduce `any`. Use `unknown` and narrow it when the input is genuinely unknown.
- Define explicit interfaces/types for data crossing a module or persistence boundary.

## Imports

- Import shared packages by package name, not by relative paths across package boundaries.
- Prefer direct imports when a library supports tree-shaking, especially for icons.
- `unplugin-icons` virtual imports belong in consuming apps. Shared packages should accept icon components or snippets.

## Formatting

- Use the nearest Prettier configuration; do not reformat unrelated files by hand.

## Shared-package boundaries

Fix a defect in the shared package instead of adding consumer-only overrides, shims or compatibility
aliases. This applies to UI/theme configuration and dependency conflicts as well as ordinary code.

## Naming

Use the local convention where one exists. The common baseline is:

| Type         | Convention             | Example                |
| ------------ | ---------------------- | ---------------------- |
| Components   | PascalCase             | `ProductCard.svelte`   |
| Utilities    | kebab-case             | `format-date.ts`       |
| Server files | kebab-case + `.server` | `product-db.server.ts` |
| Schemas      | kebab-case + `-schema` | `product-schema.ts`    |
| Types        | kebab-case + `-types`  | `product-types.ts`     |

See the [frontend guide](./frontend/README.md) for Svelte and UI rules and the [backend guide](./backend/README.md)
for database rules.
