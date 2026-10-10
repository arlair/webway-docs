# Architecture guidance

Use the target project's current architecture and nearest `AGENTS.md`. These
patterns are starting points, not a requirement to reshape every project.

## Shared principles

- Keep feature logic with its domain where the project uses `src/domain/`.
  Preserve framework entry points and existing module boundaries.
- Reuse existing shared packages and fix shared defects at their source.
  See the [coding guidelines](../coding-guidelines.md).
- Choose persistence guidance by the actual backend: [PostgreSQL, D1 or SQLite](../backend/README.md).
  Use `withSql` for PostgreSQL projects that follow that boundary, not for every database.
- Preserve strict types and validate external data using the project's existing schemas.

## Project patterns

| Work                                | Read                                      |
| ----------------------------------- | ----------------------------------------- |
| Astro sites and islands             | [Astro](./templates/astro-site.md)        |
| SvelteKit routes and server logic   | [SvelteKit](./templates/sveltekit-app.md) |
| Electron main, preload and renderer | [Electron](./templates/electron-app.md)   |
| AI generation features              | [AI generation](./ai-generation.md)       |

Find package APIs and project-specific architecture through the
[projects index](../projects/README.md). For linking, validation and releases,
read [workspace setup](../workspace-setup.md), [testing](../testing.md) or
[release guidance](../RELEASE.md) as needed.

## Active spikes

- [Dev Cockpit backend spike](./dev-cockpit-backend-spike.md) — one shared
  SwiftUI shell comparing a Swift/XPC supervisor with a Go/socket supervisor.
