# Svelte

Applies to new and changed Svelte component code in Svelte 5 projects.

- Use runes such as `$props`, `$state` and `$derived` for new component state
  and props.
- Prefer callback props for new component APIs; do not introduce
  `createEventDispatcher`.
- Do not introduce legacy `$:` or `export let` syntax in new or changed code.
  Preserve unrelated legacy components unless migration is explicitly in
  scope.
- A `$` property in configuration or an ordinary variable named `$` is not
  Svelte reactivity.

For routes, server load, form actions and hooks, also read
[`sveltekit.md`](./sveltekit.md). For shared components, read the package
documentation for [`@eldarlabs/svelte-ui`](../../../packages/svelte-ui/README.md).
