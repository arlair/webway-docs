# Standalone Documentation Strategy

> **Status:** RFC (Request for Comments)  
> **Problem:** When sites are cloned standalone (outside the monorepo), relative links to `docs/webway/` break.

---

## Problem Statement

The webway workspace uses a modular documentation structure where shared docs live in `docs/webway/` and sites reference them via relative paths:

```markdown
<!-- sites/portablecoffee/docs/README.md -->

See [Coding Guidelines](../../../docs/webway/coding-guidelines.md)
```

**This works in the monorepo** but breaks when a site is cloned standalone because `docs/webway/` doesn't exist.

### Requirements

1. Sites should be self-documenting when cloned standalone
2. Single source of truth for shared documentation (avoid copy-paste drift)
3. Minimal maintenance overhead
4. Works for both human readers and AI agents

---

## Options

### Option A: Hosted Documentation Site

Publish `docs/webway` to a hosted docs site and use absolute URLs.

**Implementation:**

- Set up GitHub Pages for `webway-docs` repo
- Use absolute URLs: `https://docs.eldarlabs.com/coding-guidelines`

**Pros:**

- Always accessible
- Single source of truth
- No local file dependencies

**Cons:**

- Requires hosting setup and maintenance
- Can't browse offline when standalone
- Extra step to keep hosted version in sync

---

### Option B: Git Submodule

Include `docs/webway` as a git submodule in each standalone site.

**Implementation:**

```bash
# In each site repo
git submodule add git@github.com:arlair/webway-docs.git docs/shared
```

**Pros:**

- Docs travel with the repo
- Version-locked to specific commit
- Works offline

**Cons:**

- Submodule complexity (clone --recursive, update commands)
- Easy to forget to update
- Nested git repos can confuse tools

---

### Option C: Template-Based Doc Generation (Recommended)

Use a Liquid template system to generate site-specific documentation at build/release time.

**Implementation:**

1. **Source docs as partials:**

   ```
   docs/webway/
   ├── _partials/              # Reusable content blocks
   │   ├── svelte5-rules.md
   │   ├── typescript-rules.md
   │   ├── database-patterns.md
   │   └── packages/
   │       ├── core.md
   │       ├── svelte-ui.md
   │       └── astro.md
   └── templates/
       └── site-readme.liquid  # Template that assembles partials
   ```

2. **Site config defines what to include:**

   ```yaml
   # sites/portablecoffee/docs/config.yaml
   name: PortableCoffee
   template: astro-site
   packages:
     - core
     - svelte-ui
     - astro
   features:
     - database
     - r2-images
   ```

3. **Template assembles the docs:**

   ```liquid
   # {{ site.name }} Documentation

   ## Architecture
   This site uses the **{{ site.template }}** pattern.

   ## Coding Standards
   {% include '_partials/svelte5-rules.md' %}
   {% include '_partials/typescript-rules.md' %}

   ## Packages
   {% for pkg in site.packages %}
   ### @eldarlabs/{{ pkg }}
   {% include '_partials/packages/' | append: pkg | append: '.md' %}
   {% endfor %}

   {% if site.features contains 'database' %}
   ## Database Patterns
   {% include '_partials/database-patterns.md' %}
   {% endif %}
   ```

4. **Output:** Self-contained `README.md` per site

**Pros:**

- Truly self-contained (no external dependencies)
- Single source of truth (partials)
- Site-specific customization via config
- Works offline, works standalone
- Can output to both local files AND hosted endpoints

**Cons:**

- Requires template system setup
- Generated files need to be committed or generated at clone time
- Slightly more complex workflow

---

### Option D: Dual Output (Local + Hosted)

Extension of Option C: template system writes to both local files and a hosted endpoint.

**Implementation:**

```liquid
{% output 'sites/{{ site.slug }}/docs/README.md' %}
{% output 'https://api.docs.eldarlabs.com/{{ site.slug }}' method: 'PUT' %}
```

**Pros:**

- Best of both worlds
- Hosted always current
- Local available for standalone

**Cons:**

- Additional infrastructure (API endpoint)
- Two places to maintain (though auto-synced)

---

### Option E: Inline Critical + Link for Details

Keep essential rules inline in each site, link to hosted for full details.

**Implementation:**

```markdown
## Quick Reference

**Svelte 5:** Use Runes only ($state, $derived, $props, $effect)
**Imports:** Direct `unplugin-icons` paths for tree-shaking (`~icons/lucide/user` or
`~icons/tabler/user`); shared packages should accept icon components instead of importing virtual
icons directly.
**Styling:** DaisyUI semantic classes (btn-primary, bg-base-100)

📚 Full docs: https://docs.eldarlabs.com
```

**Pros:**

- Simplest to implement
- Sites have enough to work with
- No generation step

**Cons:**

- Some duplication
- Risk of inline content drifting from source
- Less comprehensive for standalone use

---

## Recommendation

**Option C (Template-Based Generation)** is the most robust solution, especially since:

1. You already have a Liquid template system that can import files and write outputs
2. It provides true standalone capability
3. Maintains single source of truth
4. Can be extended to Option D (dual output) later

### Suggested Implementation Steps

1. **Phase 1:** Convert current `docs/webway/` structure to use partials
   - Move reusable content into `_partials/`
   - Keep package and template docs as-is but mark them for inclusion

2. **Phase 2:** Create site config schema
   - Define what each site needs (template, packages, features)
   - Store in each site's `docs/config.yaml`

3. **Phase 3:** Build template for site README
   - Create `templates/site-readme.liquid`
   - Test with portablecoffee

4. **Phase 4:** Integrate with release workflow
   - Regenerate docs as part of release process
   - Optionally commit generated files

5. **Phase 5 (Optional):** Add hosted output
   - Set up docs hosting endpoint
   - Add dual-output to template

---

## Open Questions

1. Should generated docs be committed to the site repos, or generated on-demand?
2. For AI agents: should they read source partials or generated output?
3. What triggers doc regeneration? (Manual, on release, on commit to webway-docs?)

---

## Related Documents

- [Architecture Overview](./architecture/README.md)
- [Workspace Setup](./workspace-setup.md)
- [Release Process](./RELEASE.md)
