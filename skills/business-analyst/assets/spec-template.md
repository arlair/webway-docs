# [Feature/Bug Name] Specification

## 0. Governance & Standards

_The following workspace standards have been applied to this specification:_

- [ ] **Architecture** ([docs/webway/architecture/](../../architecture/README.md)) - _Applied to Technical Design_
- [ ] **Project Template** ([astro-site](../../architecture/templates/astro-site.md) | [sveltekit-app](../../architecture/templates/sveltekit-app.md) | [electron-app](../../architecture/templates/electron-app.md)) - _Applied to Stack Decisions_
- [ ] **Coding Guidelines** ([docs/webway/coding-guidelines.md](../../coding-guidelines.md)) - _Applied to Implementation_
- [ ] **Testing Strategy** ([docs/webway/testing.md](../../testing.md)) - _Applied to Verification_
- [ ] **Local Project Docs** (`./docs/README.md`) - _Project specific context_

_(Agent: Check only the boxes relevant to this specific task.)_

---

## 1. Constitution & Context

- **Type:** [Feature | Bug Fix | Refactor | Architecture Change]
- **Project:** [e.g., sites/portablecoffee, apps/enlucent-app]
- **Objective:** [One sentence clear summary]
- **Workspace Impact:**
  - [ ] Local only (Changes inside this project only)
  - [ ] Shared (Requires changes to `packages/X`)

---

## 2. User Stories & Definition of Done

- **As a** [user/dev], **I want to** [action], **so that** [benefit].
- **Acceptance Criteria:**
  - [ ] [Criterion 1]
  - [ ] [Criterion 2]

---

## 3. Technical Design

### 3.1 Data & Types

```typescript
// Interface changes or Schema definitions
```

### 3.2 Logic Flow (Truth Table)

| State / Input | Condition | Resulting Action |
| ------------- | --------- | ---------------- |
| Logged In     | Has Perms | Show Admin Panel |
| Logged In     | No Perms  | Show 403 Error   |

### 3.3 Dependencies

- **Internal Packages:** [e.g., Requires update to `@eldarlabs/svelte-ui`]
- **External:** [e.g., `npm install date-fns`]

---

## 4. Impact Analysis (Blast Radius)

- **Files Modified:**
  - `sites/X/src/routes/+page.svelte`
  - `packages/svelte-ui/src/components/Button.svelte`

- **Other Projects Affected:**
  - [e.g., Changing Button in svelte-ui affects all sites using it]

- **Risks:**
  - [e.g., Breaking change to component API]

---

## 5. Implementation Plan

- [ ] **Step 1:** Create/Update Types
- [ ] **Step 2:** Implementation (Logic)
- [ ] **Step 3:** Implementation (UI)
- [ ] **Step 4:** Tests & Verification

---

## 6. Verification

### Automated Tests

- **Unit Tests:** [File path to new/updated test]
- **Smoke Tests:** [Commands to run]

### Manual Verification

1. [Step-by-step guide to verify manually]
2. [Another step]

---

## 7. QA Checklist

_From [checklist_qa.md](../references/checklist_qa.md):_

- [ ] If changing `packages/`, checked impact on all dependent projects
- [ ] No circular dependencies introduced
- [ ] New API endpoints have auth checks
- [ ] Large data sets are paginated
- [ ] Secrets handled via environment variables
- [ ] Following TypeScript strict mode
- [ ] Reusing existing components from UI library
