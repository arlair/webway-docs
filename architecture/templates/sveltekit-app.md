# SvelteKit App Template

Standard architecture for SvelteKit applications.

## Projects Using This Template

- webway-admin

---

## Tech Stack

| Layer      | Technology                   |
| ---------- | ---------------------------- |
| Framework  | SvelteKit 2                  |
| UI         | Svelte 5 (Runes API)         |
| Styling    | TailwindCSS 4 + DaisyUI 5    |
| Database   | PostgreSQL via `postgres.js` |
| Forms      | Superforms + Valibot         |
| Deployment | Node adapter / Cloudflare    |

---

## Required Packages

| Package                                          | Purpose                       |
| ------------------------------------------------ | ----------------------------- |
| [@eldarlabs/core](../packages/core.md)           | Utilities, DB access, schemas |
| [@eldarlabs/svelte-ui](../packages/svelte-ui.md) | Svelte 5 UI components        |
| [@eldarlabs/sveltekit](../packages/sveltekit.md) | SvelteKit utilities           |

---

## Folder Structure

```
src/
├── routes/               # SvelteKit routes
│   ├── +layout.svelte    # Root layout
│   ├── +page.svelte      # Homepage
│   ├── api/              # API routes
│   │   └── [resource]/
│   │       └── +server.ts
│   └── [feature]/
│       ├── +page.svelte
│       ├── +page.server.ts
│       └── [id]/
│           └── +page.svelte
│
├── domain/               # Domain-driven features
│   └── <feature>/
│       ├── <feature>-db.server.ts  # Database queries
│       ├── <feature>-schema.ts     # Valibot schemas
│       └── <Feature>Table.svelte   # Domain UI component
│
├── lib/                  # Shared utilities
│   ├── components/       # Generic UI components
│   └── server/           # Server-only utilities
│
└── app.html              # HTML template
```

---

## Data Loading

### Server Load Functions

```typescript
// +page.server.ts
import { withSql } from "@eldarlabs/core/db/sql.server";

export const load = async ({ params }) => {
  const items = await withSql(async (sql) => {
    return sql`SELECT * FROM items WHERE id = ${params.id}`;
  });

  return { items };
};
```

### API Routes

```typescript
// routes/api/items/+server.ts
import { json } from "@sveltejs/kit";
import { withSql } from "@eldarlabs/core/db/sql.server";

export const GET = async ({ url }) => {
  const items = await withSql(async (sql) => {
    return sql`SELECT * FROM items`;
  });

  return json({ items });
};
```

---

## Form Handling

Using Superforms with Valibot:

```typescript
// +page.server.ts
import { superValidate, fail } from "sveltekit-superforms";
import { valibot } from "sveltekit-superforms/adapters";
import { itemSchema } from "$domain/items/item-schema";

export const load = async () => {
  const form = await superValidate(valibot(itemSchema));
  return { form };
};

export const actions = {
  default: async ({ request }) => {
    const form = await superValidate(request, valibot(itemSchema));

    if (!form.valid) {
      return fail(400, { form });
    }

    // Process valid form data
    await saveItem(form.data);

    return { form };
  },
};
```

---

## Authentication

```typescript
// hooks.server.ts
import type { Handle } from "@sveltejs/kit";

export const handle: Handle = async ({ event, resolve }) => {
  // Check authentication
  const session = await getSession(event.cookies);
  event.locals.user = session?.user;

  return resolve(event);
};
```

---

## Development

```bash
# Start dev server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

---

## Related Documentation

- [Coding Guidelines](../../coding-guidelines.md) - Code style rules
- [Testing Strategy](../../testing.md) - Testing approach
