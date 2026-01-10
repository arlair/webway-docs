# Research Amazon Products for Article

You are researching Amazon products to recommend in an affiliate article. Your goal is to find 3-8 relevant product ASINs with context for how they should be featured.

---

## Input (fill in before running)

- **Site:** [site name, e.g., travelwebway, archlinks, portablecoffee]
- **Article Topic:** [article title or topic]
- **Target Product Category:** [e.g., "travel backpacks", "noise-cancelling headphones"]
- **User-Provided ASINs (optional):** [paste any ASINs you already have in mind]

---

## Instructions

1. **If user provided ASINs:** Research those products first and gather their official names
2. **Research popular products** in the target category:
   - Look for "best {category} 2026" recommendations from reputable sources
   - Focus on products with 4+ star ratings and 500+ reviews
   - Include a mix of price points (budget, mid-range, premium)

3. **For each product, determine:**
   - The exact Amazon US ASIN (10-character alphanumeric, starts with B)
   - The official product name
   - Why it fits this article (your rationale)
   - What role it should play (Best Overall, Budget Pick, Premium Option, etc.)

---

## Output Format

Return a JSON object that can be posted directly to `/api/proposals`:

```json
{
  "site_id": "[site name]",
  "article_id": "[article-slug]",
  "agent_id": "claude-web",
  "proposals": [
    {
      "asin": "B08N5WRWNW",
      "product_name": "Osprey Farpoint 40 Travel Backpack",
      "rationale": "Best Overall - consistently top-rated carry-on travel backpack",
      "placement_context": "Featured as hero product after intro",
      "confidence_score": 0.95
    },
    {
      "asin": "B09XYZ1234",
      "product_name": "Decathlon Forclaz 40L",
      "rationale": "Budget Pick - excellent value under $100",
      "placement_context": "Budget section",
      "confidence_score": 0.85
    }
  ]
}
```

---

## Quality Checklist

Before submitting, verify:

- [ ] All ASINs are 10 characters and start with "B"
- [ ] All ASINs are for Amazon US (amazon.com)
- [ ] Products are currently available (not discontinued)
- [ ] Mix of price points included
- [ ] Each product serves a distinct purpose/use case
- [ ] Product names are official (not abbreviated)

---

## Notes

- **Optimal Model:** GPT-4o or Claude Opus (good at simulating research)
- **Confidence Score:** 0.0-1.0 (how confident you are this product fits)
- **placement_context:** Where in the article this should appear
