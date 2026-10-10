# SvelteKit app pattern

Use the target project's manifest, `svelte.config.*` and local docs to identify
its adapter, persistence and authentication. Webway-admin and
selective-tales-admin have separate domain and data lifecycles; do not assume
one app's PostgreSQL or deployment setup applies to the other.

## Structure and boundaries

- `src/routes/`: framework route entry points, load functions and actions.
- `src/domain/`: domain logic, schemas and persistence modules where established.
- `src/lib/`: existing shared utilities and server-only modules.
- Keep route entry points thin; call the project's domain/persistence boundary.
- Use generated route types for load functions, actions and request handlers.
- Validate input with existing schemas before persistence; enforce the project's
  authentication and authorization at server boundaries.
- Reuse existing form helpers and error handling. Do not introduce a new form
  library or replace hooks based on a generic example.

## References

- [SvelteKit frontend guidance](../../frontend/sveltekit.md)
- [Database guidance](../../backend/README.md)
- [Shared SvelteKit package](../../../../packages/sveltekit/README.md)
- [Projects index](../../projects/README.md)

Run only scripts declared by the target manifest with pnpm. Use
[testing guidance](../../testing.md) to choose checks and
[release guidance](../../RELEASE.md) for deployment.
