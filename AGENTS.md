# Repository Guide

## Site Structure

- This is a single-page Hugo resume with no `content/` tree. Root `config.toml` contains the published resume copy, section enablement, colors, and contact data.
- `themes/resume/` is vendored source, not a submodule: `.gitmodules` is empty and CI checks out with `submodules: false`.
- `themes/resume/layouts/index.html` is the page entrypoint; its partials own individual resume sections.
- Theme CSS starts at `themes/resume/assets/scss/devresume.scss` and is compiled through Hugo Pipes in `layouts/partials/head.html`. Edit the SCSS source, not `resources/_gen/` output.

## Commands

- Use Hugo Extended because the theme compiles SCSS: `hugo server` previews at `http://localhost:1313/`.
- `hugo --minify` is the only executable verification and matches the GitHub Actions build. There is no package manager, test suite, linter, or formatter configured.

## Deployment

- `.github/workflows/gh-pages.yml` deploys only pushes to `master`, publishing generated `public/` to GitHub Pages.
- `static/CNAME` becomes the deployed `CNAME`; root `CNAME` is not copied by Hugo, and the two files currently contain different domains.
- `public/` and root `resources/_gen/` are ignored build output and should not be committed.
