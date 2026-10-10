# Webway Shared Guidance

Use this index to choose shared guidance by task. It is not a project index.
Read only the documents relevant to the work. Follow the nearest `AGENTS.md` first;
these documents are shared defaults, not instructions to migrate unrelated code.
Use project manifests, configuration and source to verify commands and implementation details.

| Task                                                   | Read                                        |
| ------------------------------------------------------ | ------------------------------------------- |
| Frontend, Svelte, Astro, UI                            | [Frontend index](./frontend/README.md)      |
| Backend, PostgreSQL, D1, database queries              | [Backend index](./backend/README.md)        |
| TypeScript, imports, naming, shared-package boundaries | [Coding guidelines](./coding-guidelines.md) |
| Tests and validation                                   | [Testing](./testing.md)                     |
| pnpm, workspace linking, local development             | [Workspace setup](./workspace-setup.md)     |
| Architecture and project templates                     | [Architecture](./architecture/README.md)    |
| Release and deployment                                 | [Release guide](./RELEASE.md)               |

For app, package or site-specific knowledge, use the [projects index](./projects/README.md).
Content, SEO and research work normally starts in the target project's local docs.

Architecture documents may include proposals. Check the target project's source and
local docs before treating a design as implemented. The
[standalone docs strategy](./standalone-docs-strategy.md) is an RFC, not an implemented workflow.
