# Electron app pattern

The current Electron app is [enlucent-app](../../../../apps/enlucent-app/docs/README.md).
Its [manifest](../../../../apps/enlucent-app/package.json) uses React, Mantine,
Electron, electron-vite and electron-builder. SvelteKit and Svelte UI guidance
does not apply to its renderer.

## Boundaries

- `src/main/`: Electron lifecycle and main-process handlers.
- `src/preload/`: context bridge and renderer-facing type declarations.
- `src/renderer/src/`: React UI and renderer domain code.
- `src/lib/`: existing persistence, domain and IPC support code.
- `@enlucent/engine`: shared compilation and variable resolution.

Follow existing modules before adding a new layer. Read the local SQLite and
engine documentation for persistence or compilation changes.

## IPC changes

Keep privileged operations in the main process. Expose narrowly scoped,
typed preload methods; validate incoming arguments and sender permissions in
handlers. Do not add an unrestricted channel dispatcher as a convenience API.
Keep context isolation enabled and Node integration disabled in renderers.
Clean up event subscriptions when the owning UI is disposed.

## Validation and packaging

From the app repository, choose relevant scripts from its manifest:
`pnpm typecheck`, `pnpm lint`, `pnpm build`, and the appropriate test command.
Packaging uses `build:unpack`, `build:mac`, `build:win` or `build:linux`;
check their definitions because not every platform script includes type checking.

See [React guidance](../../frontend/react.md) and [release guidance](../../RELEASE.md).
