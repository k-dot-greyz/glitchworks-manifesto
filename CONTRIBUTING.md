# Contributing to glitchworks-manifesto

Welcome. **glitchworks-manifesto** is the canonical, Markdown-only home of the GlitchWorks Manifesto — principles, tenets, and portable exports that other GlitchWorks projects can ingest without coupling to a specific runtime or monorepo layout.

For a one-line overview, see [README.md](./README.md).

---

## Repository overview

This repository is a **document-first manifesto corpus**. There is no application build, package manager, or in-repo test suite. Quality is enforced through structure, review, and the quality gates in [§ Quality gates](#quality-gates).

| Area | Technology / format |
|------|---------------------|
| Primary format | [Markdown](https://commonmark.org/) (`.md`) |
| Version control | Git |
| Manifesto body | Root and topic-scoped `.md` files (add `manifesto/` or versioned dirs as the corpus grows) |
| Structured exports | JSON or YAML **only** when they are product-facing schema for manifesto hydration (not monorepo wiring) |

### Repository layout

| Path | Purpose |
|------|---------|
| `README.md` | Repository title and short description |
| `CONTRIBUTING.md` | This file — contribution workflow and document standards |
| `manifesto/` | *(planned)* Core manifesto chapters and tenets |
| `exports/` | *(planned)* Dehydrated manifesto bundles for agents and tooling (JSON/Markdown index) |

Today the tree is intentionally minimal while the manifesto is bootstrapped. New topical content should land under `manifesto/` (or a clearly named top-level `.md` with a linked index). Update this table when you add directories.

**Monorepo consumption:** When linked from [dev-master](https://github.com/k-dot-greyz/dev-master), the submodule path is `dex/09-repos/glitchworks-manifesto`. Superproject onboarding, submodule bump scripts, and agent RAM live in dev-master — not here.

---

## Submodule boundary rule

This repository is a **public, upstream-facing** git submodule. Keep it free of parent-workspace orchestration noise.

### Do not commit

- Internal dev-master guides, dex switchboard notes, or agent session runbooks
- Fork-only SOPs, `AGENT_RAM` excerpts, or private workflow YAML
- References that assume a single developer machine path (e.g. hardcoded `~/dev/Code/...` without labeling them as examples)

### Do commit

- Manifesto prose, tenets, and changelog-style updates
- Product-facing `README.md` and `CONTRIBUTING.md`
- Portable structured exports under `exports/` with a short index `.md` describing schema version and fields

Move misplaced monorepo documentation to `dev-master/dex/03-docs/guides/`. Consumers bump the submodule pointer from the superproject after your PR merges — do not document superproject-only procedures inside this repo.

---

## GlitchWorks Agnostic Architecture Protocol (document edition)

The manifesto should remain a **portable contract**: readable by humans, parseable by agents, and ingestible by any host (IDE, CI, static site, PDF pipeline) without assuming one implementation stack.

### Zero hardcoding (dynamic configuration)

- State principles and requirements in abstract terms; use placeholders for host-specific values (`your-org`, `https://example.com`).
- When an example is unavoidable, label it explicitly (e.g. "example: Termux + Node 20").

### Polymorphism by default (interface-driven contracts)

- Describe **contracts** (required sections, heading levels, front-matter keys, export field names), not one toolchain.
- Prefer tables and bullet lists for normative rules; reserve code fences for schemas and minimal examples.

### Open piping (strict message boundaries)

- Structured exports must use versioned payloads (e.g. `manifestoVersion`, `tenets[]`) so downstream tools can validate at the edge.
- Session or planning dumps belong in `exports/` with an index doc — do not mix unstructured chat logs into core tenet files.

### Boundary validation (hostile edge)

- Treat imported excerpts and third-party quotes as untrusted: verify license and attribution before merge.
- Reject drive-by edits that weaken normative language without a stated rationale in the PR body.

### State hydration and dehydration

- Each major release should be recoverable from `exports/` or a tagged commit summary in `CHANGELOG.md` when introduced.
- Front matter (when used) should include `version`, `updated`, and optional `compatibleHosts` so agents can resume context safely.

### Graceful degradation

- Document optional vs required tenets where the manifesto allows partial adoption.
- Note failure modes (e.g. missing export file, schema mismatch) and the safe fallback (read raw Markdown only).

### Agnostic telemetry

- Describe observability of *adoption* generically (which fields an integrator should log), not a single vendor dashboard.

---

## Markdown formatting standards

- One H1 per file (`#` title); use `##` and below for structure.
- Lines wrap sensibly; avoid trailing spaces (enforced by `git diff --check`).
- Fenced code blocks must declare a language tag when not plain text.
- Links: prefer relative paths inside the repo; use full GitHub URLs for external canonical repos.
- Headings: use sentence case unless the manifesto defines a proper noun style guide.

Optional local config (add in a follow-up PR if the maintainers want CI):

```json
{
  "extends": "markdownlint/style/prettier"
}
```

---

## Quality gates

Run these from the **repository root** before every commit and PR:

| Gate | Command / action | Pass criteria |
|------|------------------|---------------|
| Clean tree | `git status` | Only intentional files staged |
| Whitespace / conflict markers | `git diff --check` | No errors |
| Diff scope | `git diff --name-status origin/main` | Only manifesto-related paths |
| Markdown lint | `npx markdownlint-cli2 "**/*.md"` *(optional if installed)* | No errors on changed files |
| Boundary audit | Manual review | No dev-master/dex paths, secrets, or monorepo SOPs |
| Render sanity | Open changed `.md` in preview | Headings, tables, and fences render correctly |

If `markdownlint-cli2` is not installed:

```bash
npx --yes markdownlint-cli2 "**/*.md"
```

For a single changed file:

```bash
npx --yes markdownlint-cli2 "CONTRIBUTING.md"
```

---

## Contribution workflow

### 1. Configure remotes

```bash
git remote -v

# If you use a personal fork, add upstream:
# git remote add upstream https://github.com/k-dot-greyz/glitchworks-manifesto.git
```

### 2. Create a branch

```bash
git fetch origin
git checkout -b docs/your-topic-name origin/main
```

Use prefixes: `docs/`, `fix/`, `chore/`. Example baseline branch: `docs/add-contributing-workflow`.

### 3. Author changes

- One logical change per PR when possible (one chapter, one export schema bump, or one standards fix).
- Do not commit secrets, tokens, or private session exports.
- Update the [repository layout](#repository-layout) table when adding directories.

### 4. Run quality gates

See [Quality gates](#quality-gates).

### 5. Pre-commit checklist

1. `git status` — no misplaced monorepo or `.env` files.
2. `git diff --check` — clean.
3. Optional: `npx markdownlint-cli2` on touched Markdown.
4. Confirm export JSON/YAML matches the documented schema version in the index `.md`.

### 6. Commit and push

```bash
git add CONTRIBUTING.md   # plus your manifesto files
git commit -m "docs(manifesto): short imperative summary"
git push -u origin HEAD
```

Use [Conventional Commits](https://www.conventionalcommits.org/): `docs`, `fix`, `chore`, with a scope (`manifesto`, `exports`, `contributing`).

### 7. Open a pull request

```bash
gh pr create --repo k-dot-greyz/glitchworks-manifesto --base main \
  --title "docs(contributing): short summary" \
  --body "$(cat <<'EOF'
## Summary
- …

## Test plan
- [ ] `git diff --check`
- [ ] `npx markdownlint-cli2` on changed Markdown (if available)
- [ ] Manual preview of headings, tables, and links
- [ ] No monorepo-only paths or secrets in diff
EOF
)"
```

---

## Structured documentation exports

When adding machine-readable exports under `exports/`:

| Requirement | Detail |
|-------------|--------|
| Schema version | Top-level `manifestoVersion` or `schemaVersion` field |
| Index | `exports/README.md` listing files, purpose, and compatibility |
| Validation | Document required keys; reject unknown required fields at integrator edge |
| Human source of truth | Normative prose stays in `manifesto/`; exports are dehydrated snapshots |

Example minimal export shape (illustrative):

```json
{
  "manifestoVersion": "0.1.0",
  "updated": "2026-06-04",
  "tenets": [
    { "id": "zero-hardcoding", "title": "Zero Hardcoding", "summary": "…" }
  ]
}
```

---

## What to contribute

| Type | Guidance |
|------|----------|
| New tenet or chapter | Under `manifesto/` with cross-links from a root index when introduced |
| Wording / clarity | Small diffs; explain intent in the PR |
| Export schema | Version bump + index update + note in PR test plan |
| Corrections | Fix broken links and stale references to GlitchWorks projects |

---

## Recovering from a boundary leak

If monorepo-only documentation was committed by mistake:

```bash
git fetch origin
git reset --soft origin/main
# Move files to dev-master/dex/03-docs/guides/ or discard
git restore --staged <file>
git commit -m "docs: remove misplaced monorepo documentation"
git push origin <branch> --force-with-lease
```

---

*Keep the manifesto portable, precise, and host-agnostic — the map lives here; the territory lives in the repos that adopt it.*
