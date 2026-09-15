# AI Generation

Status: lightweight cross-project architecture guidance

This document belongs in the shared Webway documentation repository.

## Purpose

Provide just enough common direction for AI-assisted generation across projects without creating a framework before it is needed.

Likely uses include:

- Selective Tales: encounter ideas, narrative text, dialogue and player-choice wording.
- RationalDev: programming/AI-learning article ideas, drafts and editorial passes.
- Webway admin: coffee, travel and other site content.
- Future projects that need similar generation/review features.

Selective Tales is the first proving implementation. RationalDev is a likely second implementation because it exercises a different kind of generation workflow.

## Core principles

### Use AI SDK rather than wrapping model APIs ourselves

Use the Vercel AI SDK (`ai`) as the default TypeScript model interface.

Use a provider package or compatible provider configuration only when required.

Do not create our own abstraction for:

- basic text generation;
- structured output;
- streaming;
- tool calling;
- provider request/response protocols.

### Start inside the project

Do not create a shared `packages/ai-*` package yet.

Start AI code inside the project that needs it. Extract shared code only after at least two real implementations demonstrate that the same code is genuinely reusable.

For Selective Tales, AI code should initially live inside `sites/selective-tales-admin`.

### Prefer plain functions over frameworks

A multi-step content process does not require a workflow engine.

Prefer ordinary TypeScript functions such as:

```text
generateIdeas()
draftArticle()
critiqueDraft()
generateEncounterIdeas()
```

Only introduce a reusable workflow abstraction after repeated code makes the benefit obvious.

### Keep domain logic in the project

Shared AI infrastructure should never own game, coffee, travel or article-domain decisions.

Project code owns:

- prompts;
- domain context;
- schemas;
- validation;
- editorial/game rules;
- review UI;
- persistence decisions.

### Use structured output where code benefits from it

Use structured output when application code needs to reason about the result.

Examples:

- encounter idea candidates;
- article outlines;
- product-copy fields;
- proposed player choices.

Do not force every creative response into a large schema.

### AI does not own authoritative data

Models may propose creative content, but application/database rules remain authoritative.

For Selective Tales, an early generator must not silently invent valid-looking game state, IDs, rewards or transitions.

For content sites, a model must not invent product IDs, affiliate URLs, prices or other canonical facts.

### Keep prompts in Git initially

Prompt files should live with the project code.

Do not build a prompt CMS yet.

A simple prompt/version constant is sufficient at first. Add more formal prompt history only when there is a concrete need to compare or reproduce older generations.

### Keep stable prompt material first

When prompts become substantial, prefer this order:

```text
stable instructions/style/examples
project/domain context
current task/input
```

Avoid putting per-request timestamps, random IDs or other changing values at the beginning.

Do not build application-level KV caching. Let the provider handle prompt caching.

### Provider/model choice is configuration

Do not spread provider/model strings throughout feature code.

Initially, a small config file or exported constant is enough.

Do not introduce a model-role hierarchy until multiple workflows make it useful.

### Persistence is earned by the use case

A smoke test does not need a new run-history schema just to prove AI SDK works.

Persist runs when persistence gives us something useful, such as:

- reviewing candidates later;
- comparing models/prompts;
- tracking accepted content;
- diagnosing costs;
- asynchronous/batch work.

Reuse an existing suitable persistence model if one already exists.

### Agents are optional

Do not use Pi, Codex, Claude Code, OpenCode or DeepSeek Harness as the production runtime merely because a process has multiple stages.

They may be useful development tools.

Use agent/tool behaviour in the application only when a task genuinely needs dynamic decisions rather than a predictable sequence of functions.

### Local-first admin is fine

`selective-tales-admin` and `webway-admin` may run locally for now.

Keep provider keys server-side and avoid needless macOS-specific coupling in core logic so deployment can change later if required.

## First proving use case: Selective Tales

The first task is not to redesign encounters.

Selective Tales already has prototypes, documentation and game modelling. The coding agent must inspect those before deciding how AI generation should connect to them.

Start with:

1. repository/documentation reconnaissance;
2. a minimal AI SDK provider integration;
3. a small AI test page;
4. one structured generation example;
5. no automatic mutation of real game data.

After that works, decide whether the next step should use the real encounter model.

## Likely second proving use case: RationalDev

RationalDev is a useful second implementation because it tests editorial generation rather than game content.

A future workflow may include:

```text
topic / learning notes
→ article ideas
→ selected angle
→ outline
→ draft
→ style / AI-ism checks
→ human review
```

When RationalDev work begins, first inspect any existing article-generation guidance and prompts. Improve or reuse them rather than replacing them blindly.

If Selective Tales and RationalDev end up needing the same small helper, that is evidence for extraction into `packages/`.

## Later Webway adoption

`sites/webway-admin` is currently being upgraded, so AI integration there should wait.

When it is ready, reuse lessons from the earlier implementations for coffee, travel and other content workflows.

Do not force Selective Tales into `webway-admin`. Separate admin applications may share proven packages without becoming one application.

## Deliberate non-goals

For now:

- no shared AI framework;
- no generic workflow engine;
- no prompt CMS;
- no generic run/audit database;
- no universal model-role system;
- no batch scheduler;
- no agent runtime;
- no automatic publishing;
- no premature cloud deployment work.

Every new abstraction should answer a concrete problem already present in the code.
