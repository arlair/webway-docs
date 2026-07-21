# Astro Site Template

Standard architecture for Astro 5 static sites with Svelte 5 islands.

## Projects Using This Template

- portablecoffee
- travelwebway
- archlinks
- rationaldev
- enlucent

---

## Tech Stack

| Layer      | Technology                                             |
| ---------- | ------------------------------------------------------ |
| Framework  | Astro 5 (Static Site Generation + Island Architecture) |
| UI Islands | Svelte 5 (Runes API)                                   |
| Styling    | TailwindCSS 4 + DaisyUI 5                              |
| Database   | PostgreSQL via `postgres.js` (where applicable)        |
| Deployment | Cloudflare Pages                                       |

---

## Required Packages

| Package                                          | Purpose                        |
| ------------------------------------------------ | ------------------------------ |
| [@eldarlabs/core](../packages/core.md)           | Utilities, DB access, schemas  |
| [@eldarlabs/svelte-ui](../packages/svelte-ui.md) | Svelte 5 UI components         |
| [@eldarlabs/astro](../packages/astro.md)         | Astro layouts and integrations |

---

## Folder Structure

```
src/
├── content/              # Astro content collections
│   ├── posts/            # Blog posts (MDX)
│   └── config.ts         # Collection schemas
│
├── domain/               # Domain-driven features
│   └── <feature>/
│       ├── <feature>.ts           # Business logic
│       ├── <Feature>.svelte       # Interactive component
│       └── <feature>-db.server.ts # Database access
│
├── pages/                # Astro routes
│   ├── index.astro
│   ├── blog/
│   │   ├── [...page].astro        # Paginated listing
│   │   └── [slug].astro           # Individual post
│   └── rss.xml.js                 # RSS feed
│
├── layouts/              # Page layouts (if not using @eldarlabs/astro)
│
└── lib/                  # Shared utilities
    └── config.ts         # Site configuration
```

---

## Build Configuration

### Environment-Based Builds

```bash
# Development (with draft posts, local images)
npm run dev

# Stage build
npm run build:stage

# Production build
npm run build:prod
```

### Astro Config

```javascript
// astro.config.mjs
import { defineConfig } from "astro/config";
import svelte from "@astrojs/svelte";
import tailwind from "@astrojs/tailwind";
import cloudflare from "@astrojs/cloudflare";

export default defineConfig({
  integrations: [svelte(), tailwind()],
  output: "hybrid",
  adapter: cloudflare(),
});
```

---

## Content Collections

### Post Schema

```typescript
// src/content/config.ts
import { defineCollection, z } from "astro:content";

const posts = defineCollection({
  type: "content",
  schema: z.object({
    title: z.string(),
    description: z.string(),
    date: z.date(),
    tags: z.array(z.string()).optional(),
    draft: z.boolean().optional(),
  }),
});

export const collections = { posts };
```

### Draft Filtering

```astro
---
// Filter drafts in production
const posts = await getCollection('posts', ({ data }) => {
  return import.meta.env.DEV || !data.draft
})
---
```

---

## Svelte Islands

Use `client:*` directives for interactive components:

```astro
---
import SearchModal from '@eldarlabs/svelte-ui/search/SearchModal.svelte'
---

<!-- Only hydrate when visible -->
<SearchModal client:visible />

<!-- Hydrate on page load -->
<Counter client:load initialCount={0} />

<!-- Hydrate on idle -->
<Newsletter client:idle />
```

---

## Deployment

### Cloudflare Pages

```bash
# Deploy to staging
npm run deploy:stage

# Deploy to production
npm run deploy:prod
```

See [RELEASE.md](../../RELEASE.md) for the full release process.

---

## Related Documentation

- [Coding Guidelines](../../coding-guidelines.md) - Code style rules
- [Testing Strategy](../../testing.md) - Testing approach
- [Image Hosting](../../infrastructure/image-hosting.md) - R2 image architecture
