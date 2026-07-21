# Interview Strategies

## 1. Scenario: User Reports a Bug
*Target Output: Reproduction Steps & Root Cause Hypothesis*
1.  **Environment:** "Is this happening locally, in dev, or prod?"
2.  **Reproduction:** "What are the exact steps to make this fail?"
3.  **Expectation:** "What *should* have happened vs what *did* happen?"
4.  **Recent Changes:** "Have you updated any shared `packages/` recently?"

## 2. Scenario: User Wants a Feature
*Target Output: User Story & Logic Constraints*
1.  **The "Why":** "What problem are we solving for the user?"
2.  **The "Where":** "Does this logic live in the App or a shared Package?"
3.  **Data:** "Do we need to store new data or just display existing data?"
4.  **UI/UX:** "Do you have a design in mind, or should I propose a layout based on `ui-kit`?"

## 3. Scenario: Architecture/Refactor
*Target Output: Safety & Pattern Alignment*
1.  **Motivation:** "Is this for performance, readability, or preparation for a future feature?"
2.  **Constraints:** "Are there any 'Forbidden Refactors' (e.g. don't touch the legacy auth module)?"
3.  **Blast Radius:** "Which other apps in the workspace depend on this code?"
