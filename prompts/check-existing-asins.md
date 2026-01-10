# Check Existing ASINs Before Adding

Before submitting product proposals, check which ASINs already exist in the database to avoid duplicates.

---

## Endpoint

```
POST /api/products/check-asins
```

## Request

```json
{
  "asins": ["B08N5WRWNW", "B09XYZ1234", "B07ABCDEFG"]
}
```

## Response

```json
{
  "existing": [
    {
      "asin": "B08N5WRWNW",
      "title": "Osprey Farpoint 40 Travel Backpack",
      "image_url": "https://...",
      "in_database": true
    }
  ],
  "new": ["B09XYZ1234", "B07ABCDEFG"]
}
```

---

## Workflow

1. **Before proposing ASINs:** Call this endpoint with your list
2. **For existing ASINs:** You can still propose them for a different article
3. **For new ASINs:** These will need to be imported after approval

---

## UI Access

You can also use the web interface at:

```
http://localhost:6100/check-asins
```

Paste ASINs in the textarea (one per line or comma-separated) and click "Check ASINs".
