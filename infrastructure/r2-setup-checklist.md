# R2 Setup Checklist

One-time setup for Cloudflare R2 image hosting infrastructure.

## Prerequisites

- [x] Cloudflare account with R2 enabled
- [x] Domain(s) configured in Cloudflare DNS

---

## Phase 1: Create Buckets

### Public Images Bucket

- [x] Create bucket named `public-images`
- [x] Location: choose nearest region (or Auto)
- [x] Note the bucket endpoint URL

### Protected Images Bucket (Optional)

- [x] Create bucket named `protected-images`
- [x] This is for auth-protected content (premium content, game assets, etc.)

---

## Phase 2: Custom Domain Setup

For each site that needs image hosting:

### Add Custom Domain

1. [ ] Go to R2 → `public-images` → Settings → Custom Domains
2. [ ] Add domain: `images.{sitename}.com`
3. [ ] Verify DNS is proxied through Cloudflare (orange cloud)
4. [ ] Wait for SSL certificate provisioning

### Sites to Configure

| Site                 | Custom Domain               | Status |
| -------------------- | --------------------------- | ------ |
| portablecoffee       | `images.portablecoffee.com` | [x]    |
| travelwebway         | `images.travelwebway.com`   | [x]    |
| rationaldev          | `images.rationaldev.com`    | [x]    |
| archlinks            | `images.archlinks.com`      | [x]    |
| enlucent             | `images.enlucent.com`       | [x]    |
| (add more as needed) |                             |        |

---

## Phase 3: API Credentials

### Create API Token

1. [ ] Go to Cloudflare Dashboard → R2 → Manage R2 API Tokens
2. [ ] Create token with permissions:
   - Object Read & Write for `public-images`
   - Object Read & Write for `protected-images` (if applicable)
3. [ ] Note the credentials:
   - Access Key ID
   - Secret Access Key
   - Account ID

### Configure in webway-admin

Add to `.env` (or environment config):

```env
R2_ACCOUNT_ID=your_account_id
R2_ACCESS_KEY_ID=your_access_key
R2_SECRET_ACCESS_KEY=your_secret_key
R2_BUCKET_NAME=public-images
R2_ENDPOINT=https://<account_id>.r2.cloudflarestorage.com
```

---

## Phase 4: CORS Configuration (If Needed)

If uploading directly from browser via presigned URLs:

1. [ ] Go to R2 → `public-images` → Settings → CORS
2. [ ] Add rule:

```json
[
  {
    "AllowedOrigins": ["http://localhost:5174", "https://admin.yourdomain.com"],
    "AllowedMethods": ["GET", "PUT", "HEAD"],
    "AllowedHeaders": ["*"],
    "MaxAgeSeconds": 3600
  }
]
```

---

## Phase 5: Verify Setup

### Test Custom Domain

```bash
# After uploading a test file, verify it's accessible
curl -I https://images.portablecoffee.com/portablecoffee/posts/test/hero.webp
```

Expected: `200 OK` with proper content-type and cache headers.

### Test API Access

```bash
# List objects in bucket
aws s3 ls s3://public-images/ \
  --endpoint-url https://<account_id>.r2.cloudflarestorage.com
```

---

## Phase 6: Cache Headers

R2 custom domains respect `Cache-Control` headers set on objects.

When uploading, set:

```javascript
const uploadParams = {
  Bucket: "public-images",
  Key: "portablecoffee/posts/slug/hero.webp",
  Body: buffer,
  ContentType: "image/webp",
  CacheControl: "public, max-age=31536000, immutable",
}
```

---

## Verification Checklist

After setup, verify:

- [ ] Can list objects via S3 API
- [ ] Can upload objects via S3 API
- [ ] Custom domain returns images with 200 OK
- [ ] Cache headers are correct (`public, max-age=31536000`)
- [ ] SSL working on custom domains
- [ ] CORS allows uploads from admin UI (if using presigned URLs)

---

## Troubleshooting

### Custom Domain Returns 404

- Verify the object exists at the expected path
- Check that domain is proxied (orange cloud) in DNS
- Path is case-sensitive

### CORS Errors on Upload

- Check CORS configuration includes your dev origin
- Ensure `AllowedMethods` includes `PUT`

### SSL Certificate Pending

- Can take up to 24 hours (usually minutes)
- Ensure domain is proxied through Cloudflare
