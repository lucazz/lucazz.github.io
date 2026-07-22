# Remove Google Analytics Template Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Restore Hugo builds by removing the reference to Hugo's deleted Google Analytics internal template.

**Architecture:** Keep the existing scripts partial and remove only the unavailable internal template call. Preserve the FontAwesome script and `googleAnalytics` configuration, then remove the stale failure note from `AGENTS.md`.

**Tech Stack:** Hugo, Go templates

## Global Constraints

- Keep the FontAwesome script unchanged.
- Keep the existing `googleAnalytics` configuration unchanged.
- Do not add a replacement analytics integration.

---

### Task 1: Remove the Legacy Template Call

**Files:**

- Modify: `themes/resume/layouts/partials/scripts.html:5`
- Modify: `AGENTS.md:14`

**Interfaces:**

- Consumes: Hugo's rendering of `scripts.html` from `themes/resume/layouts/index.html`.
- Produces: A scripts partial that renders without the removed Hugo internal template.

- [ ] **Step 1: Reproduce the failing build**

Run:

```bash
hugo --minify
```

Expected: exit 1 with `no such template "_internal/google_analytics_async.html"` from `themes/resume/layouts/partials/scripts.html`.

- [ ] **Step 2: Remove only the unavailable template call**

Delete this line from `themes/resume/layouts/partials/scripts.html`:

```go-template
    {{ template "_internal/google_analytics_async.html" . }}
```

- [ ] **Step 3: Remove the stale failure guidance**

Delete this bullet from `AGENTS.md`:

```markdown
- The build currently fails with Hugo `0.162.1` because `themes/resume/layouts/partials/scripts.html` references the removed `_internal/google_analytics_async.html` template. Do not report a clean build until that incompatibility is fixed.
```

- [ ] **Step 4: Confirm the removed template is no longer referenced**

Run:

```bash
if rg -n '_internal/google_analytics_async\.html' themes/resume/layouts; then exit 1; fi
```

Expected: exit 0 with no output.

- [ ] **Step 5: Verify the CI-equivalent build passes**

Run:

```bash
hugo --minify
```

Expected: exit 0 and Hugo's site build summary. Hugo `0.162.1` may still print the existing `languageCode` deprecation warning.

- [ ] **Step 6: Review the focused change**

Run:

```bash
git diff --check
git status --short
```

Expected: no whitespace errors; only the requested template removal, `AGENTS.md`, and approved design and plan documents are changed.
