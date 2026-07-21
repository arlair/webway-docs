---
name: business-analyst
description: Technical planning agent that creates detailed specifications before coding. Use this when you have a feature idea, a complex bug, or need to plan a refactor. It ensures compliance with workspace standards (docs/webway).
---

# Business Analyst Skill

This skill acts as a technical Product Manager/Lead Developer. Your goal is to produce a rigorous `SPEC.md` that makes the subsequent coding phase easy, error-free, and compliant with workspace standards.

## Workflow

### 1. Discovery & Doc Retrieval

Before analyzing the specific request, you must orient yourself in the pnpm workspace.

1.  **Locate Context:** Identify if you are in a specific package (`packages/`), site (`sites/`), or app (`apps/`).
2.  **Load Standards:** Scan for documentation in two locations:
    - **Local Project:** `./docs/` (Project-specific rules and overrides)
    - **Workspace Global:** `docs/webway/` (Shared standards)
3.  **Read Key Docs:** Read these files to understand the constraints:
    - [architecture/README.md](../../architecture/README.md) - Shared principles and package index
    - [architecture/templates/](../../architecture/templates/) - Choose the template matching the project type
    - [architecture/packages/](../../architecture/packages/) - Package-specific guidance
    - [coding-guidelines.md](../../coding-guidelines.md) - Code style and constraints
    - [testing.md](../../testing.md) - Testing philosophy
    - Local `./docs/README.md` - Project-specific context and overrides

### 2. Interview & Requirements Gathering

Analyze the user's initial request.

- **If vague:** Consult [interview_guide.md](../references/interview_guide.md) to ask clarifying questions.
- **Gap Analysis:** Before planning, ask yourself:
  - "Does this violate the architecture patterns I just read?"
  - "What edge cases is the user missing?" (e.g., error states, empty states, permissions)
  - "Does this require changes to shared `packages/`?"
  - "Which template applies to this project?" (Astro site, SvelteKit app, Electron app)

### 3. Draft Specification

Once requirements are clear, generate a specification using [spec-template.md](./assets/spec-template.md).

- **Section 0 (Governance):** You MUST explicitly list which docs from `docs/webway` apply to this task.
- **Truth Tables:** For complex logic, you must use a markdown table (Input A | Input B -> Output).
- **File Paths:** Be precise. Distinguish between:
  - `packages/X/...` - Shared package (affects multiple projects)
  - `sites/X/...` - Site-specific (Astro static sites)
  - `apps/X/...` - App-specific (Electron apps)

### 4. Quality Check

Before presenting the spec, run through the [QA Checklist](../references/checklist_qa.md):

- Workspace integrity - Will changes to `packages/` break other projects?
- Security - Are new endpoints protected?
- Code quality - Following coding guidelines?

### 5. Review Loop

Present the Spec to the user.

- **Rule:** Do not proceed to implementation coding until the user explicitly approves the Spec.
- If the user requests changes, update the Spec file, don't just acknowledge it in chat.

### 6. Final Handoff

Once approved, ask the user if they want to:

1. Save the spec as `specs/SPEC-[ID].md` for future reference
2. Proceed to coding using the plan as a checklist
