# Release & Deployment Guide

Quick reference for commits, releases, and deployments using the workflow system.

---

## Quick Commands

| Task               | Command                                    |
| ------------------ | ------------------------------------------ |
| Scan all changes   | `./scripts/scan-repo-changes.sh`           |
| Scan specific site | `./scripts/scan-repo-changes.sh archlinks` |
| Build stage        | `cd sites/archlinks && pnpm build:stage`   |
| Build prod         | `cd sites/archlinks && pnpm build:prod`    |
| Preview locally    | `cd sites/archlinks && pnpm preview:stage` |
| Deploy stage       | `cd sites/archlinks && pnpm deploy:stage`  |
| Deploy prod        | `cd sites/archlinks && pnpm deploy:prod`   |

---

## Workflow Options

### Option 1: WIP Commit (No Release)

For saving work-in-progress without versioning:

```
Ask agent: "Commit my changes in archlinks"
```

Agent will:

1. Show changes
2. Create commit with `wip(scope): message`
3. Optionally push to remote

### Option 2: Full Release

For versioned releases with deployment:

```
Ask agent: "Release archlinks"
```

Agent will:

1. Scan archlinks + detect dependencies with changes
2. Suggest version bumps (PATCH/MINOR/MAJOR)
3. Update package.json versions
4. Commit with conventional messages
5. Build and verify
6. Deploy to Cloudflare
7. Publish packages to GitHub npm registry

---

## Release Checklist

1. **Scan changes**

   ```bash
   ./scripts/scan-repo-changes.sh archlinks
   ```

2. **Review and approve changes**
   - Check diffs are what you expect
   - Note which dependencies need releasing

3. **Bump versions** (in dependency order)

   ```bash
   cd packages/core && npm version patch --no-git-tag-version
   cd packages/astro && npm version patch --no-git-tag-version
   cd packages/svelte-ui && npm version patch --no-git-tag-version
   cd sites/archlinks && npm version patch --no-git-tag-version
   ```

4. **Update dependency references**
   - Update `@eldarlabs/*` versions in archlinks package.json

5. **Build and verify**

   ```bash
   cd sites/archlinks && pnpm build:prod
   ```

6. **Commit and push**

   ```bash
   # Each package
   cd packages/core && git add -A && git commit -m "chore(core): v1.0.x" && git push
   # ... repeat for astro, svelte-ui, archlinks
   ```

7. **Deploy**

   ```bash
   cd sites/archlinks && npx wrangler pages deploy deploy/prod/public --project-name=archlinks
   ```

8. **Publish packages**
   ```bash
   cd packages/core && npm publish
   cd packages/astro && npm publish
   cd packages/svelte-ui && npm publish
   ```

---

## Dependency Version Sync

When releasing a site, verify that `@eldarlabs/*` dependencies are in sync with the latest published versions.

### Check Current Versions

```bash
# Check what versions a site is using vs what's published
SITE=archlinks
echo "=== $SITE dependency versions ==="
grep -E '"@eldarlabs/' sites/$SITE/package.json

echo "=== Published package versions ==="
for pkg in core astro svelte-ui; do
  echo "@eldarlabs/$pkg: $(cat packages/$pkg/package.json | grep '"version"' | cut -d'"' -f4)"
done
```

### Review Changes Between Versions

If a package was updated, review what changed before updating the site:

```bash
# Example: Check svelte-ui changes between 1.0.1 and 1.0.2
cd packages/svelte-ui
git log --oneline v1.0.1..v1.0.2  # If using tags
# OR
git log --oneline --since="2024-01-01" --until="2024-01-15"  # By date range
```

### Update Site Dependencies

After verifying changes are compatible:

1. Update version in site's `package.json`
2. Run `pnpm install` to update lockfile
3. Build and test: `pnpm build:stage`
4. Include in site's release commit

### Sync Checklist

Before releasing a site, verify:

- [ ] Site uses latest `@eldarlabs/core` version (or document why not)
- [ ] Site uses latest `@eldarlabs/astro` version
- [ ] Site uses latest `@eldarlabs/svelte-ui` version
- [ ] Reviewed changelogs for any breaking changes
- [ ] Tested build with updated dependencies

---

## Cloudflare Pages Setup

### Branch Configuration

Cloudflare Pages uses **branch names** to determine environments:

| Branch Flag      | Environment | URL Pattern                    |
| ---------------- | ----------- | ------------------------------ |
| `--branch=main`  | Production  | `sitename.com` (custom domain) |
| `--branch=stage` | Preview     | `stage.sitename.pages.dev`     |

> **Important**: Production branch in CF Pages must be `main`, regardless of your git default branch (e.g., `master`).

### Deploy Scripts

Sites should have these scripts in `package.json`:

```json
{
  "deploy:stage": "wrangler pages deploy deploy/stage/public --project-name=SITENAME --branch=stage --commit-dirty=true",
  "deploy:prod": "wrangler pages deploy deploy/prod/public --project-name=SITENAME --branch=main --commit-dirty=true"
}
```

### Stage Testing Workflow

1. Build stage: `pnpm build:stage`
2. Deploy to stage: `pnpm deploy:stage`
3. Verify at: `https://stage.sitename.pages.dev`
4. If approved, build and deploy prod

### Custom Domain Setup

1. Go to **CF Dashboard → Workers & Pages → Project → Custom domains**
2. Add domain (e.g., `sitename.com`)
3. CF will auto-configure DNS if domain is already in CF
4. **Purge cache** after domain changes (see Tips below)

---

## Workflow Prompts

Agent prompts are in `.agent/workflows/`:

| File                 | Purpose                           |
| -------------------- | --------------------------------- |
| `scan-changes.md`    | Find changes across repos         |
| `review-changes.md`  | Code review + test suggestions    |
| `wip-commit.md`      | Commit without release            |
| `prepare-release.md` | Version bump + changelog          |
| `commit-changes.md`  | Commit with conventional messages |
| `build-verify.md`    | Build + baseline check            |
| `deploy.md`          | Push + deploy to Cloudflare       |

---

## Tips

- **Stage before prod**: Always test with `pnpm build:stage` first
- **Dependency order**: Release packages before sites that use them
- **Search index**: Build script now runs pagefind automatically
- **Environment**: Uses `.env` for local builds, CF dashboard for cloud
- **Cache purging**: After domain changes or major updates, purge CF cache:
  - Go to **CF Dashboard → sitename.com → Caching → Configuration → Purge Everything**
  - Or hard refresh browser: `Cmd + Shift + R`
