# Coding Guidelines

Technical constraints and code style rules for the webway workspace.

---

## 1. Svelte 5 (Runes Only)

All Svelte code **must** use the Svelte 5 Runes API. Legacy syntax is forbidden.

### ✅ Correct - Svelte 5 Runes

```svelte
<script lang="ts">
  // Props
  let { title, onClick } = $props()

  // Reactive state
  let count = $state(0)

  // Derived values
  let doubled = $derived(count * 2)

  // Side effects
  $effect(() => {
    console.log('Count changed:', count)
  })
</script>
```

### ❌ Forbidden - Legacy Svelte 3/4

```svelte
<script lang="ts">
  // Never use these patterns
  export let title           // Use $props() instead
  $: doubled = count * 2     // Use $derived() instead
  $: console.log(count)      // Use $effect() instead

  import { createEventDispatcher } from 'svelte'
  const dispatch = createEventDispatcher()  // Use callback props instead
</script>
```

---

## 2. TypeScript

### Strict Mode

All projects use TypeScript strict mode. No exceptions.

### Semicolons

**Do not use semicolons** unless required by the compiler (rare edge cases).

```typescript
// ✅ Correct
const name = "value";
function doThing() {
  return result;
}

// ❌ Incorrect
const name = "value";
function doThing() {
  return result;
}
```

### No `any` Type

Never use `any`. Use `unknown` if the type is truly unknown, then narrow it.

```typescript
// ✅ Correct
function parse(input: unknown): Result {
  if (typeof input === "string") {
    return processString(input);
  }
  throw new Error("Invalid input");
}

// ❌ Incorrect
function parse(input: any): Result {
  return processString(input);
}
```

---

## 3. Imports

### Direct Imports for Tree-Shaking

Use direct imports, especially for icons. In apps configured with `unplugin-icons`, import the
specific icon collection and icon name. This keeps icons tree-shakeable and allows Lucide and
Tabler icons to be used consistently.

```typescript
// ✅ Correct - unplugin-icons direct imports
import User from "~icons/lucide/user";
import Search from "~icons/tabler/search";

// ❌ Incorrect - Barrel import
import { User, Search } from "lucide-svelte";
```

`unplugin-icons` is configured by the consuming app. Published shared packages should accept
icon components through props/snippets rather than importing virtual `~icons/*` modules.

### Package Imports

Use the package name, not relative paths across packages:

```typescript
// ✅ Correct
import { withSql } from "@eldarlabs/core/db/sql.server";

// ❌ Incorrect (relative path to another package)
import { withSql } from "../../../packages/core/src/db/sql.server";
```

---

## 4. Styling

### TailwindCSS 4 + DaisyUI 5

Use utility classes from TailwindCSS and component classes from DaisyUI.

```svelte
<!-- ✅ Correct - DaisyUI semantic classes -->
<button class="btn btn-primary">Submit</button>
<div class="card bg-base-100 shadow-xl">Content</div>
<input class="input input-bordered w-full" />

<!-- ❌ Incorrect - Raw color values -->
<button class="bg-blue-500 text-white px-4 py-2">Submit</button>
```

### Semantic Color Tokens

Use semantic color names that respect theming:

| Token               | Use For                    |
| ------------------- | -------------------------- |
| `text-primary`      | Primary text color         |
| `bg-base-100`       | Base background            |
| `bg-base-200`       | Slightly darker background |
| `text-base-content` | Default text               |
| `border-base-300`   | Borders                    |

---

## 5. No Workarounds Policy

Diffs and changes should solve the **root cause** of an issue rather than applying overrides or workarounds.

### Theme and UI

- **Do not** use manual CSS variable overrides to force a theme to work
- **Do** investigate why the underlying framework is not picking up configuration
- **Do** fix the configuration (e.g., `tailwind.config.ts`, versions) to align with standard usage

### Dependency Management

- **Do not** add shim code to make conflicting libraries coexist
- **Do** choose the preferred library and remove the conflicting one

### Example

```css
/* ❌ Workaround - Don't do this */
:root {
  --p: var(--color-primary); /* Aliasing to make old code work */
}

/* ✅ Fix - Update the code to use the correct token */
```

---

## 6. Database Access

### Use `withSql` Wrapper

Always use the `withSql` wrapper from `@eldarlabs/core` for database operations:

```typescript
import { withSql } from "@eldarlabs/core/db/sql.server";

const items = await withSql(async (sql) => {
  return sql`SELECT * FROM items WHERE status = 'active'`;
});
```

### Define DTOs

Always define interfaces for database results:

```typescript
interface ItemDTO {
  id: string;
  name: string;
  created_at: Date;
}

const items = await withSql(async (sql) => {
  return sql<ItemDTO[]>`SELECT id, name, created_at FROM items`;
});
```

---

## 7. File Naming

| Type         | Convention             | Example                |
| ------------ | ---------------------- | ---------------------- |
| Components   | PascalCase             | `ProductCard.svelte`   |
| Utilities    | kebab-case             | `format-date.ts`       |
| Server files | kebab-case + `.server` | `product-db.server.ts` |
| Schemas      | kebab-case + `-schema` | `product-schema.ts`    |
| Types        | kebab-case + `-types`  | `product-types.ts`     |
