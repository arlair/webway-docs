# SvelteKit

Use for SvelteKit routes, server load functions, form actions, hooks and
server-only modules.

- Read the project's local authentication, data and deployment guidance before
  changing server route behavior.
- Keep server-only dependencies and secrets on the server side of the route
  boundary.
- Put route actions and server data access in the framework's server modules;
  keep browser components focused on rendering and interaction.
- Apply the [Svelte guidance](./svelte.md) to component changes.

For the workspace's shared SvelteKit package and app structure, read the
[SvelteKit architecture template](../architecture/templates/sveltekit-app.md)
and [`@eldarlabs/sveltekit` package docs](../../../packages/sveltekit/README.md).
