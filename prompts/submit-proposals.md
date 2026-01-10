# Submit Product Proposals

Submit product recommendations to the proposals queue for human review.

---

## Endpoint

```
POST /api/proposals
```

## Request Format

```json
{
  "site_id": "travelwebway",
  "article_id": "best-travel-backpacks",
  "agent_id": "claude-opus",
  "proposals": [
    {
      "asin": "B08N5WRWNW",
      "product_name": "Osprey Farpoint 40 Travel Backpack",
      "rationale": "Best Overall - consistently top-rated carry-on travel backpack",
      "placement_context": "Featured as hero product after intro",
      "confidence_score": 0.95
    }
  ]
}
```

## Field Reference

| Field         | Required | Description                                            |
| ------------- | -------- | ------------------------------------------------------ |
| `site_id`     | Yes      | Site identifier (e.g., travelwebway, archlinks)        |
| `article_id`  | No       | Article slug this is for (e.g., best-travel-backpacks) |
| `agent_id`    | No       | Which AI/agent submitted this (for tracking)           |
| `proposals[]` | Yes      | Array of product proposals                             |

### Proposal Fields

| Field               | Required | Description                                   |
| ------------------- | -------- | --------------------------------------------- |
| `asin`              | Yes      | Amazon US ASIN (10 chars, starts with B)      |
| `product_name`      | No       | Official product name (helps human reviewers) |
| `rationale`         | No       | Why this product fits the article             |
| `placement_context` | No       | Where in the article it should go             |
| `confidence_score`  | No       | 0.0-1.0 confidence rating                     |

## Response

```json
{
  "created": 3,
  "duplicates": 1
}
```

---

## After Submission

1. Proposals appear in the review UI at `/proposals`
2. Humans can approve or reject with feedback
3. On approval, product gets imported to the database

---

## UI Access

View and manage proposals at:

```
http://localhost:6100/proposals
```
