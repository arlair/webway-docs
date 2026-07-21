# Image Hosting Architecture

A centralized image hosting solution for multiple sites using Cloudflare R2.

## Overview

Host images for ~10 websites using Cloudflare R2, with custom domains per site and support for both public content and auth-protected game assets.

### Goals

- Centralized image storage across all sites
- Custom domain per site (e.g., `images.portablecoffee.com`)
- Local development workflow with live/local switching
- Future-proof for selling individual sites
- Auth-protected assets for game project
- Multi-machine development with R2 as source of truth

---

## Architecture

### Per-Site Bucket Strategy

Each site gets its own R2 bucket for clean URLs and complete isolation:

```
┌────────────────────────────────────────────────────────────────────────┐
│                         R2 Buckets (one per site)                      │
├────────────────────────────────────────────────────────────────────────┤
│                                                                        │
│  rationaldev-images/              portablecoffee-images/               │
│    ├── posts/{slug}/hero.webp       ├── posts/{slug}/hero.webp         │
│    └── assets/logo.webp             ├── assets/logo.webp               │
│          ▼                          └── products/{id}.webp             │
│   images.rationaldev.com                   ▼                           │
│                                   images.portablecoffee.com            │
│                                                                        │
│  travelwebway-images/             archlinks-images/                    │
│    ├── posts/{slug}/hero.webp       └── posts/{slug}/hero.webp         │
│    └── assets/og-default.webp              ▼                           │
│          ▼                          images.archlinks.com               │
│   images.travelwebway.com                                              │
│                                                                        │
└────────────────────────────────────────────────────────────────────────┘
```

### URL Structure

With per-site buckets, URLs are clean (no site prefix):

```
https://images.rationaldev.com/posts/my-article/hero.webp
                               └──────────────────────────┘
                               Key in rationaldev-images bucket
```

### Protected Content (Optional)

For auth-protected content (premium, game assets), use a separate bucket:

```
┌─────────────────────────────────────────────────────────────┐
│                    R2: protected-images                     │
├─────────────────────────────────────────────────────────────┤
│  {site}/                                                    │
│    └── premium/{content-id}/asset.webp                      │
│                                                             │
│  game/                                                      │
│    ├── scenes/{uuid}/background.jpg                         │
│    └── items/{uuid}.png                                     │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
              Cloudflare Worker (auth layer)
                          │
                          ▼
              Protected endpoints
              (signed URLs + rate limiting)
```

### Why Per-Site Buckets?

| Benefit                | Description                                                        |
| ---------------------- | ------------------------------------------------------------------ |
| **Clean URLs**         | No site prefix in path (`/posts/...` not `/rationaldev/posts/...`) |
| **True isolation**     | Images only accessible via their site's domain                     |
| **Site transfer**      | Move entire bucket when selling a site                             |
| **Per-site billing**   | R2 dashboard shows storage per bucket                              |
| **Independent limits** | One site's traffic doesn't affect others                           |

---

## Folder Structure

Each site has a consistent folder structure in R2:

```
{site}/
  ├── posts/
  │     └── {post-slug}/
  │           └── hero.webp
  │
  ├── assets/                    # Site-wide assets (logos, og-images, etc.)
  │     ├── logo.webp
  │     ├── og-default.webp
  │     └── author-avatar.webp
  │
  └── products/                  # Product images (if applicable)
        └── {product-id}.webp
```

### Folder Naming

| Folder      | Purpose                   | Examples                                     |
| ----------- | ------------------------- | -------------------------------------------- |
| `posts/`    | Post-specific images      | Hero images, inline content images           |
| `assets/`   | Site-wide reusable images | Logos, favicons, social cards, author photos |
| `products/` | E-commerce product images | Product thumbnails, gallery images           |

---

## Database Schema

Images are tracked in Postgres for metadata and sync status:

```sql
CREATE TABLE images (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  site_id TEXT NOT NULL,
  relative_path TEXT NOT NULL,  -- "posts/brewing-guide/hero.webp"

  -- R2 state (source of truth once uploaded)
  r2_uploaded_at TIMESTAMPTZ,   -- NULL = not yet in R2
  r2_etag TEXT,                 -- For sync/change detection

  -- Metadata
  size_bytes INTEGER,
  alt_text TEXT,

  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),

  UNIQUE(site_id, relative_path)
);

CREATE INDEX idx_images_site ON images(site_id);
CREATE INDEX idx_images_pending ON images(site_id) WHERE r2_uploaded_at IS NULL;
```

### Key Design Decisions

- **UUID v4**: Standard `gen_random_uuid()`, no need for time-ordered v7
- **No local_path**: Compute at runtime from `relative_path` + configured storage root
- **R2 as source of truth**: Local DB is a cache that can be rebuilt from R2
- **No lineage tracking**: Duplicated images across sites are independent copies

---

## Multi-Machine Development

### Primary Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Cloudflare R2                            │
│                  (Source of Truth)                          │
└─────────────────────────────────────────────────────────────┘
                          ▲
          Upload          │          Sync
    ┌─────────────────────┼─────────────────────┐
    │                     │                     │
┌───┴───┐           ┌─────┴─────┐         ┌─────┴─────┐
│ Main  │◄─────────►│ Secondary │         │   New     │
│  Dev  │   LAN     │    Mac    │         │ Machine   │
│ Mac   │  Postgres │           │         │           │
└───────┘           └───────────┘         └───────────┘
    │
    ├── Postgres (primary)
    └── Local image storage
```

### Workflow

1. **Main dev machine**: Runs Postgres, stores images locally, uploads to R2
2. **Secondary machines**: Connect to main Postgres over LAN, or sync from R2
3. **New machine setup**: "Sync from R2" command to pull images and populate local DB

### Sync from R2

Use R2's S3-compatible API to list and fetch objects:

```typescript
// List objects, optionally filter by LastModified
const response = await r2.listObjectsV2({
  Bucket: "public-images",
  Prefix: "portablecoffee/",
})

// Filter options:
// - By site (prefix)
// - By date range (LastModified)
// - By path pattern
```

---

## Public Sites Setup

### Custom Domain Configuration

For each site, add a custom domain in R2:

1. Cloudflare Dashboard → R2 → `public-images` bucket → Settings → Custom Domains
2. Add domain: `images.{sitename}.com`
3. SSL is auto-provisioned

### URL Structure

```
https://images.portablecoffee.com/portablecoffee/posts/brewing-guide/hero.webp
https://images.travelwebway.com/travelwebway/posts/japan-trip/hero.webp
```

> [!NOTE]
> R2 custom domains don't support path prefix stripping natively. Options:
>
> - Accept redundant path (`images.site.com/site/...`)
> - Use a Worker for cleaner URLs (`images.site.com/posts/...`)
> - Use separate buckets per site (more buckets, cleaner URLs)

---

## Local Development

### Environment-Based Switching

```javascript
// lib/images.js
const IMAGE_BASE = import.meta.env.DEV
  ? "/images" // Served by webway-admin
  : "https://images.{site}.com"

export function getImageUrl(site, path) {
  return `${IMAGE_BASE}/${path}`
}
```

### Local Image Serving

During development, webway-admin serves images from local storage at `/images/[site]/[...path]`.

---

## Admin UI Features

Build in the SvelteKit admin project:

### Image Import & Upload

- Import images from external sources (existing)
- Manual "Publish to R2" per image or batch selection
- Presigned URL upload for direct R2 writes
- WebP conversion via Sharp (1200px max width)

### Image Browser

- List images per site with sync status
- Filter: unsynced only, by date range, by path
- Preview with size info
- Copy URL to clipboard

### Publish Workflow

| Status  | Indicator | Meaning                     |
| ------- | --------- | --------------------------- |
| Pending | 🔄        | Local only, not in R2       |
| Synced  | ✅        | Uploaded to R2              |
| Failed  | ❌        | Upload attempted but failed |

---

## Image Processing

### Current Approach (Simple)

- Single WebP variant at 1200px max width, 80% quality
- No additional variants (hero images are ~60KB)

### Future Enhancement (If Needed)

Generate multiple variants:

```javascript
const variants = [
  { width: 1200, suffix: "-1200", format: "webp" },
  { width: 600, suffix: "-600", format: "webp" },
  { width: 400, suffix: "-thumb", format: "webp" },
]
```

---

## Protected Images (Auth-Required)

### Strategy: Signed URLs + Obfuscated Paths

1. **Obfuscated paths**: Use UUIDs instead of descriptive names
2. **Signed URLs**: Time-limited tokens
3. **Rate limiting**: Prevent enumeration attacks

### Caching Behavior

| Asset Type         | Cache Strategy             | Reason                      |
| ------------------ | -------------------------- | --------------------------- |
| Public images      | `public, max-age=31536000` | Same for everyone           |
| Signed URL images  | `public, max-age=3600`     | URL is unique per token     |
| Cookie-auth images | `private, no-store`        | Must not leak between users |

---

## Site Sale/Transfer

When selling a site:

1. Export the site's folder from R2
2. Provide the exported archive to buyer
3. Buyer uploads to their own storage
4. Buyer points `images.site.com` DNS to new location
5. All URLs continue working (no content changes needed)

---

## Cost Estimate

| Resource       | Free Tier    | Expected Usage | Monthly Cost |
| -------------- | ------------ | -------------- | ------------ |
| R2 Storage     | 10 GB        | < 2 GB         | $0           |
| R2 Class A ops | 1M/month     | < 100k         | $0           |
| R2 Class B ops | 10M/month    | < 500k         | $0           |
| Workers        | 100k req/day | < 10k/day      | $0           |
| Custom Domains | Unlimited    | ~12            | $0           |

**Total: Free** (at current scale)

Beyond free tier:

- R2 Storage: $0.015/GB/month
- Workers: $5/month for 10M requests
