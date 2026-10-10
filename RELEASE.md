# Release guidance

Release behavior is project-specific. Read the target project's `AGENTS.md`,
local docs and `package.json` scripts before running release commands.

## Sequence

1. Scan the requested repository or workspace and confirm release scope.
2. Review the diff and identify shared-package dependencies.
3. Prepare versions and changelogs in dependency order.
4. Run the target project's checks and build.
5. Commit approved changes in each nested repository.
6. Push, publish or deploy only after an explicit user request.

## Boundaries

- Run git commands in the repository that owns the files; the workspace root is
  a shell repository around separate app, package, site and docs repositories.
- Release shared packages before consumers when their published artifacts
  changed.
- Do not bump versions for content or documentation unless the target project
  requires it.
- Do not bypass hooks, publish packages or deploy failed builds.
- If a project supports staged deployment, verify stage before production.

## Cloudflare Pages

Cloudflare Pages deployment is not uniform across the workspace. Use only
scripts defined by the target project, such as `build:stage`, `deploy:stage`,
`build:prod` or `deploy:prod`, and read its runtime/staging guide first.

## Finding commands

Use the target repository's scripts and local release documentation. The old
`.agent/workflows/` references are not present in this checkout. For a workspace
change scan, inspect `scripts/scan-changes.mjs` at the workspace root before using it.
