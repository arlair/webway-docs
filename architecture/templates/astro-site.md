# Astro site pattern

Applies to Astro projects such as portablecoffee, travelwebway, archlinks,
rationaldev and enlucent. Read the target manifest and `astro.config.*`;
output mode, content loaders and deployment differ between sites.

## Configuration and structure

- Preserve the target site's `src/pages/`, content collections and domain structure.
- Inspect its `src/content.config.ts` (or existing collection configuration)
  before adding content; use its schemas and loaders.
- Portablecoffee currently uses static output; Travelwebway uses server output
  with the Cloudflare adapter. Do not copy an adapter or output mode between them.
- Their Tailwind setup uses `@tailwindcss/vite`; preserve the existing integration.
- Follow the [workspace linking guidance](../../workspace-setup.md) for linked
  Svelte packages and [frontend guidance](../../frontend/README.md) for islands and styling.

Use shared [Astro](../../../../packages/astro/README.md),
[Svelte UI](../../../../packages/svelte-ui/README.md) and
[core](../../../../packages/core/README.md) APIs where already applicable.
This list is not an instruction to add dependencies.

## Content and islands

Keep server/build-time data access outside browser islands. Choose hydration
(`client:load`, `client:idle`, `client:visible`) for the interaction needed.
Use the site's existing draft/publication filtering; development mode alone
may not describe stage and production publication rules.

## Commands and deployment

Run pnpm scripts declared by the target manifest. Confirm whether it has
`build`, `build:stage`, `build:prod` or other scripts before running them.
Read local runtime/data lifecycle docs before building or deploying: a build
may depend on a database snapshot or other prepared inputs.

See [release guidance](../../RELEASE.md) and
[image hosting](../../infrastructure/image-hosting.md) for those tasks.
