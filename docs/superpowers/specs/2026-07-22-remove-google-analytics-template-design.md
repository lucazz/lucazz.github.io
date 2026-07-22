# Remove Google Analytics Template Design

## Change

- Remove the `_internal/google_analytics_async.html` call from `themes/resume/layouts/partials/scripts.html`.
- Keep the unrelated FontAwesome script and existing `googleAnalytics` configuration unchanged.
- Remove the resulting stale build-failure note from `AGENTS.md`.

## Verification

- Run `hugo --minify` and require a successful build.
- Confirm the generated page contains no reference to the legacy Google Analytics template.
