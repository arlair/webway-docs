# Workspace Setup & Development Guide

This repository uses **pnpm workspaces** to manage multiple projects (sites, apps, packages) in a single monorepo. This setup allows for efficient dependency management and easy local development of shared libraries.

## Directory Structure

- **`apps/`**: Electron or other applications.
- **`sites/`**: Web sites (e.g., `portablecoffee`).
- **`packages/`**: Shared libraries (e.g., `svelte-ui`, `core`).
- **`docs/`**: Project documentation.

## Git Repository Structure

**Crucial:** While this functions as a pnpm monorepo for dependencies, **it is composed of multiple independent git repositories**.

- **Root (`/`)**: Tracks only workspace configuration (pnpm-lock.yaml, global docs, etc.).
- **Sub-projects (`sites/*`, `packages/*`)**: Each is a **separate git repository**.

### Workflow Implications

1.  **Context Matters**: You must `cd` into the specific directory to run git commands for that project.
    - ❌ `git status` at root → Shows only root config changes.
    - ✅ `cd sites/archlinks && git status` → Shows site changes.
2.  **No Atomic Commits**: You cannot commit changes across multiple sites/packages in one go. You must commit to each repo individually.
3.  **Submodules**: The root repo likely tracks these as submodules (or separate clones), meaning the root knows "where" the sub-repos are pointing, but doesn't track their content directly.

## How Workspaces Work

We use `pnpm` to link packages locally.

### Dependency Resolution

In `package.json` files, you might see dependencies defined in two ways:

1.  **`"workspace:*"`**: Explicitly tells pnpm to _only_ use the local workspace version. If it's missing, installation fails.
2.  **`"1.0.0"` (Explicit Version)**: Tells pnpm to use version `1.0.0`.
    - **Locally**: If a package in the workspace matches this version (e.g., `packages/svelte-ui` has `"version": "1.0.0"`), pnpm will **automatically link to the local copy**. This is the magic of pnpm workspaces.
    - **Standalone/CI**: If the workspace package is _not_ present (e.g., on a build server cloning only one site), pnpm will attempt to fetch `1.0.0` from the npm registry (if published).

**We use explicit versions (Option 2)** in our sites to support both local development (auto-linked) and standalone builds.

### Version Consistency

It is critical that shared dependencies (like `svelte`) use the **exact same version** across the entire workspace to avoid "duplicate instance" errors.

- We use **`pnpm.overrides`** in the root `package.json` to enforce this consistency globally.

```json
// root package.json
"pnpm": {
  "overrides": {
    "svelte": "^5.28.2"
  }
}
```

## Configuration & "Why do we need explicit includes?"

You might notice explicit path configurations in `astro.config.mjs` and `tsconfig.json`, such as:
`include: ['../../packages/svelte-ui/**/*.svelte']`

### The Reason

In previous versions or configurations (often with `preserveSymlinks: true`), build tools were "tricked" into thinking linked packages were just standard files inside `node_modules`.

However, for robust modern builds (especially with Svelte 5+ and Vite), we disable `preserveSymlinks`. This means tools resolve files to their **real physical location** on disk (e.g., `../../packages/svelte-ui`).

- Since these files are **outside the project root** (`sites/portablecoffee`), security and performance defaults in tools like Vite and TypeScript often ignore them.
- We must explicitly tell the tools: "Yes, please process the files in this external directory."

### Dynamic Configuration

To support standalone builds where `../../packages` might not exist, we use dynamic logic in our config files:

```javascript
// astro.config.mjs
const localLib = path.resolve(__dirname, "../../packages/svelte-ui");
if (fs.existsSync(localLib)) {
  // Only include if it exists locally
  include.push("../../packages/svelte-ui/**/*.svelte");
}
```

```

### "Why is Svelte special? What about core/astro?"

You might wonder why `@eldarlabs/core` (TypeScript) and `@eldarlabs/astro` (Astro components) work without this extra config.

1.  **`@eldarlabs/core`**: These are standard TypeScript/JavaScript files. Vite (and Node) are designed to handle JS/TS imports from `node_modules` (or symlinked packages) automatically. They don't require a special compiler plugin with strict include/exclude rules.
2.  **`@eldarlabs/astro`**: The Astro compiler handles `.astro` files. It is generally configured to process any `.astro` file it encounters in the dependency graph, regardless of location.
3.  **`@eldarlabs/svelte-ui`**: `.svelte` files are **not** valid JavaScript. They *must* be compiled by `vite-plugin-svelte`. This plugin has a performance optimization where it **only** compiles files in your `include` list (defaulting to `src/`) and explicitly **ignores** `node_modules` (where symlinks usually live). Because we are linking, we have to manually tell the plugin: "Hey, look in this external folder too!"

## Development Workflow

1.  **Install**: Run `pnpm install` at the root. This installs dependencies for _all_ projects and links them.
2.  **Dev**: Run `pnpm dev:portablecoffee` (or other scripts defined in root `package.json`) to start development.
3.  **Build**:
    - **Root**: `pnpm build:all` builds everything.
    - **Site**: `cd sites/portablecoffee && npm run build:dev`.

## Troubleshooting

- **"Interface declarations..." / Parse Errors**: Usually means an external component is being treated as plain JS instead of Svelte/TS. Check `astro.config.mjs` includes.
- **Duplicate Svelte Instances**: Check `npm list svelte`. If duplicates exist, update the root `pnpm.overrides`.
```
