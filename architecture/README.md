# Architecture Overview

This directory contains modular architecture documentation for the webway workspace.

## Shared Principles

All projects in this workspace follow these core principles:

### 1. Domain-Driven Structure

Code is organized by business domain in `src/domain/`:

```
src/domain/
├── <domain-name>/
│   ├── <feature>.ts          # Logic
│   ├── <Feature>.svelte      # UI component
│   └── <feature>-db.server.ts # Database access
```

### 2. No Duplication

Use the shared `@eldarlabs/*` packages instead of duplicating logic:

- Utilities and business logic → `@eldarlabs/core`
- UI components → `@eldarlabs/svelte-ui`
- Astro layouts → `@eldarlabs/astro`

### 3. Database Access

- PostgreSQL via `postgres.js`
- Use `withSql` wrapper from `@eldarlabs/core` for connection management
- Define DTOs/interfaces for all query results

### 4. Type Safety

- TypeScript strict mode
- No `any` types
- Runtime validation with Valibot schemas

---

## Packages

Individual package documentation:

- [@eldarlabs/core](../../../packages/core/README.md) - Framework-agnostic utilities
- [@eldarlabs/svelte-ui](../../../packages/svelte-ui/README.md) - Svelte 5 UI components
- [@eldarlabs/astro](../../../packages/astro/README.md) - Astro layouts and integrations
- [@eldarlabs/sveltekit](../../../packages/sveltekit/README.md) - SvelteKit utilities
- [@eldarlabs/toolkit](../../../packages/toolkit/README.md) - Build tools
- [@enlucent/engine](../../../packages/enlucent-engine/README.md) - Template compilation and variable resolution engine
- [enlucent-cli](../../../apps/enlucent-cli/README.md) - Standalone native CLI build application

---

## Project Templates

Choose the template that matches your project type:

| Template                                      | Projects                                                       |
| --------------------------------------------- | -------------------------------------------------------------- |
| [Astro Site](./templates/astro-site.md)       | portablecoffee, travelwebway, archlinks, rationaldev, enlucent |
| [SvelteKit App](./templates/sveltekit-app.md) | webway-admin                                                   |
| [Electron App](./templates/electron-app.md)   | enlucent-app                                                   |

---

## Related Documentation

- [Workspace Setup](../workspace-setup.md) - pnpm workspaces and linking
- [Coding Guidelines](../coding-guidelines.md) - Code style and constraints
- [Testing Strategy](../testing.md) - Testing philosophy
- [Release Process](../RELEASE.md) - Versioning and deployment
