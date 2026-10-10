# React

Use when the target project's configuration identifies React as its renderer.

- Keep Electron main/preload/IPC concerns separate from React renderer code;
  use the [Electron architecture template](../architecture/templates/electron-app.md)
  for those boundaries.
- Prefer existing shared components and package APIs over consumer-local
  copies.
