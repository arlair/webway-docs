# Workspace Setup and Local Development

Use for pnpm linking, standalone project checkouts and local workspace behaviour.

## Repository shape

- The root is a shell repository for workspace configuration and scripts.
- Projects under `apps/*`, `packages/*`, `sites/*` and `docs/*` are separate git repositories.
  Shared guidance is owned by the nested `docs/webway/` repository; run its git commands there.
- Run project commands and git operations from the target repository. A root git diff does not
  represent all nested-repository changes.

## pnpm configuration

- The root pins `pnpm@12.3.4` and declares `apps/*`, `packages/*` and `sites/*` as workspaces.
- `pnpm-workspace.yaml` is authoritative for workspace settings: `nodeLinker: hoisted`, peer
  installation, build approvals and local package overrides.
- The root overrides the listed `@eldarlabs/*` packages to `workspace:*` for local development.
- This checkout contains more than one dependency style (`workspace:*`, package-local `link:` and
  published version ranges). Check the target manifest before changing dependency declarations.

Most current sites, including Travelwebway, use `workspace:*` in this checkout. A standalone clone
does not have the root workspace overrides, so its own manifest and lockfile determine resolution;
do not assume a local workspace link exists there.

## Common commands

From the workspace root:

```bash
pnpm install
pnpm run build:packages
pnpm run build:sites
pnpm run build:apps
pnpm run build:all
pnpm run test:packages
```

Project-specific scripts belong to the project's `package.json` and nearest `AGENTS.md`. Prefer a
filtered project command over a broad workspace command when validating one project.

## Linked Svelte packages

Astro projects consuming linked `@eldarlabs/svelte-ui` may need to include the package's `.svelte`
files in the Svelte/Vite configuration and exclude it from dependency optimization. Inspect the
target project's config before changing it. If a consumer appears to resolve stale compiled package
files after a shared-package build, run `pnpm install` and then follow that package's build/export
workflow.
