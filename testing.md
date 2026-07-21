# Testing Strategy

Our testing strategy prioritizes **confidence** and **maintainability** over coverage metrics. We aim to avoid brittle tests that require constant updates when UI implementation details change.

---

## Core Philosophy

> "Focus on Pure Logic Unit Tests (for utilities that rarely change but are critical) and High-Level Smoke Tests (to ensure the site actually builds and renders)."

---

## 1. Pure Logic Unit Tests

**Goal:** Verify complex business logic and critical utilities.

**Scope:** Functions that are pure (deterministic output for a given input) and have high complexity or risk.

**Location:** Primarily in `@eldarlabs/core` or domain-specific logic files (e.g., `src/lib/utils`, `src/domain/*/logic.ts`).

### Do Test

- Data transformation functions
- Complex calculations (e.g., pricing, dates)
- Regex patterns and validators
- Schema validation logic

### Don't Test

- UI components (unless they contain complex internal state logic isolated from the DOM)
- Simple pass-through functions
- Framework-provided functionality

### Example

```typescript
// utils/format-price.ts
export function formatPrice(cents: number, currency = "AUD"): string {
  return new Intl.NumberFormat("en-AU", {
    style: "currency",
    currency,
  }).format(cents / 100);
}

// utils/format-price.test.ts
import { describe, it, expect } from "vitest";
import { formatPrice } from "./format-price";

describe("formatPrice", () => {
  it("formats cents to AUD currency", () => {
    expect(formatPrice(1999)).toBe("$19.99");
  });

  it("handles zero", () => {
    expect(formatPrice(0)).toBe("$0.00");
  });
});
```

---

## 2. High-Level Smoke Tests

**Goal:** Ensure the application builds, deploys, and renders critical pages without crashing.

**Scope:** The critical path of the user journey.

**Tools:** Playwright (recommended) or simple build-time checks.

### Do Test

- Build process completes successfully (`pnpm build`)
- Homepage loads (status 200)
- Critical pages (e.g., "Shop", "Blog") render key elements
- No console errors on critical paths
- Forms submit without JavaScript errors

### Don't Test

- Pixel-perfect rendering (unless doing visual regression)
- Every single interaction on every page
- Implementation details (e.g., "div with class 'foo' exists")

### Example (Playwright)

```typescript
// tests/smoke.spec.ts
import { test, expect } from "@playwright/test";

test("homepage loads successfully", async ({ page }) => {
  await page.goto("/");
  await expect(page).toHaveTitle(/Site Name/);
  await expect(page.locator("h1")).toBeVisible();
});

test("blog listing renders posts", async ({ page }) => {
  await page.goto("/blog");
  const posts = page.locator('[data-testid="post-card"]');
  await expect(posts).toHaveCount({ min: 1 });
});
```

---

## What We Avoid

### Brittle UI Unit Tests

Tests that break whenever a class name or HTML structure changes:

```typescript
// ❌ Brittle - Depends on implementation details
expect(wrapper.find(".card-header__title--large")).toExist();

// ✅ Better - Tests behavior or accessibility
expect(screen.getByRole("heading", { name: "Product Title" })).toBeVisible();
```

### Mock-Heavy Integration Tests

Tests that require extensive mocking of internal APIs, which often drift from reality:

```typescript
// ❌ Over-mocked - Doesn't test real behavior
jest.mock("../../lib/api");
jest.mock("../../lib/auth");
jest.mock("../../lib/config");
// ... 10 more mocks

// ✅ Better - Test against real (or realistic) dependencies
// Use a test database, or test the actual API
```

---

## Test File Organization

```
src/
├── domain/
│   └── products/
│       ├── product-utils.ts
│       └── product-utils.test.ts    # Co-located unit tests
│
tests/
├── smoke.spec.ts                    # High-level smoke tests
├── e2e/                             # End-to-end tests (if needed)
│   └── checkout.spec.ts
└── fixtures/                        # Test data
    └── products.json
```

---

## Running Tests

```bash
# Unit tests (Vitest)
npm run test

# Smoke tests (Playwright)
npm run test:e2e

# Watch mode for development
npm run test:watch
```

---

## When to Add Tests

1. **New utility function** → Add unit test
2. **Bug fix** → Add regression test that would have caught it
3. **New critical page** → Add smoke test
4. **Complex business logic** → Add comprehensive unit tests

---

## When NOT to Add Tests

1. **Simple component** that just renders props
2. **One-off scripts** that run once
3. **Wrapper components** that just pass through
4. **Styling changes** (unless visual regression is set up)
