# Frontend guidance

Read only the stack guidance relevant to the files being changed. Project
documentation and configuration override this shared baseline.

| Task                                   | Read                                                                                     |
| -------------------------------------- | ---------------------------------------------------------------------------------------- |
| Svelte component                       | [`svelte.md`](./svelte.md)                                                               |
| SvelteKit route, load, action or hook  | [`sveltekit.md`](./sveltekit.md)                                                         |
| Astro page, content or island          | [`astro.md`](./astro.md)                                                                 |
| React renderer                         | [`react.md`](./react.md)                                                                 |
| Tailwind or DaisyUI styling            | [`styling.md`](./styling.md)                                                             |
| Electron main/preload/IPC architecture | [`../architecture/templates/electron-app.md`](../architecture/templates/electron-app.md) |

For Svelte consumers, prefer `@eldarlabs/svelte-ui` over duplicating shared
components. Import shared packages by package name. `unplugin-icons` virtual
imports belong in consuming apps; shared packages accept caller-provided icon
components or snippets.
