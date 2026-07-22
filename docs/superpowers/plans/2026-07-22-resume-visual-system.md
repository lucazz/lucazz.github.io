# Resume Visual System Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the Bootstrap-based DevResume presentation with the approved semantic, monochrome, SF-style visual system while preserving all Hugo content and section behavior.

**Architecture:** Migrate every layout partial to a shared semantic class contract, then replace the theme stylesheet with a standalone CSS Grid and Flexbox implementation compiled through Hugo Pipes. Once the site builds and renders without Bootstrap, delete the now-unused vendored Bootstrap source.

**Tech Stack:** Hugo Extended, Go templates, SCSS, FontAwesome

## Global Constraints

- Preserve all resume copy, Hugo configuration keys, section enablement, section order, and theme attribution.
- Preserve every partial, including currently disabled education, awards, projects, and information sections.
- Remove Bootstrap and the hosted Roboto request. Keep Hugo Pipes and FontAwesome.
- Replace Hugo's deprecated `languageCode` configuration and `.Site.LanguageCode` template access with `locale` and `.Site.Language.Locale` in the root site and vendored example site.
- Replace stale root, example-site, theme, and README descriptions that claim the site uses Bootstrap; preserve attribution and license URLs.
- Use `-apple-system`, `BlinkMacSystemFont`, "Segoe UI", and `sans-serif`; only 400 and 500 font weights; and `-0.15px` letter spacing.
- Limit type sizes to 12px, 13px, 14px, and 24px.
- Use `#292929`, `#5D5D5D`, and `#9E9E9E` for text hierarchy, `rgba(41, 41, 41, 0.1)` for borders, and `#F6F6F5` for the screen background.
- Use a 16px screen frame radius, a 14px profile-image radius, and no decorative shadow.
- Keep the profile image at 80px on desktop and 64px below 768px.
- Use a 3:1 content/sidebar grid on desktop; stack content below 768px and in print.
- Do not change external link behavior or FontAwesome icon configuration beyond sizing contact and social icons to 14px.

---

### Task 1: Semantic Templates And Standalone Visual System

**Files:**

- Modify: `config.toml`
- Modify: `themes/resume/exampleSite/config.toml`
- Modify: `themes/resume/layouts/index.html`
- Modify: `themes/resume/layouts/partials/head.html`
- Modify: `themes/resume/layouts/partials/header.html`
- Modify: `themes/resume/layouts/partials/profile.html`
- Modify: `themes/resume/layouts/partials/contact.html`
- Modify: `themes/resume/layouts/partials/summary.html`
- Modify: `themes/resume/layouts/partials/experience.html`
- Modify: `themes/resume/layouts/partials/projects.html`
- Modify: `themes/resume/layouts/partials/information.html`
- Modify: `themes/resume/layouts/partials/sidebar.html`
- Modify: `themes/resume/layouts/partials/education.html`
- Modify: `themes/resume/layouts/partials/awards.html`
- Modify: `themes/resume/layouts/partials/skills.html`
- Modify: `themes/resume/layouts/partials/languages.html`
- Modify: `themes/resume/layouts/partials/interests.html`
- Modify: `themes/resume/layouts/partials/social.html`
- Modify: `themes/resume/layouts/partials/footer.html`
- Modify: `themes/resume/assets/scss/devresume.scss`

**Interfaces:**

- Consumes: Existing `.Site.Params` data, enable flags, i18n keys, Markdown rendering, profile image, and FontAwesome icon class values.
- Produces: Semantic classes owned by `devresume.scss`: `site-shell`, `resume`, `resume-header`, `resume-identity`, `resume-name`, `resume-tagline`, `resume-contact`, `resume-contact-list`, `resume-contact-item`, `resume-contact-icon`, `resume-link`, `resume-divider`, `resume-intro`, `resume-profile-image`, `resume-summary`, `resume-body`, `resume-main`, `resume-aside`, `resume-section`, `resume-section-heading`, `resume-item`, `resume-item-heading`, `resume-item-title`, `resume-item-meta`, `resume-item-content`, `resume-list`, `resume-detail-list`, `resume-detail-item`, `resume-social-list`, `resume-social-item`, `resume-social-icon`, `resume-footer`, `site-footer`, and `muted`.

- [ ] **Step 1: Record the failing structural checks**

Run:

```bash
rg -n 'bootstrap/scss/bootstrap|fonts.googleapis.com|class="[^"]*(container|row|col-|py-|px-|mb-|mr-|ml-|text-|d-|list-|media|rounded|shadow)' themes/resume/assets/scss/devresume.scss themes/resume/layouts
```

Expected: matches for the Bootstrap import, Roboto request, and utility-heavy classes. This is the RED check proving the migration is not already complete.

- [ ] **Step 2: Replace deprecated Hugo language APIs**

In both `config.toml` and `themes/resume/exampleSite/config.toml`, rename the existing `languageCode` key to `locale` without changing its value. In `themes/resume/layouts/index.html`, change:

```go-html-template
<html lang="{{ .Site.LanguageCode }}">
```

to:

```go-html-template
<html lang="{{ .Site.Language.Locale }}">
```

- [ ] **Step 3: Remove the hosted font request without changing Hugo Pipes**

Delete only these lines from `themes/resume/layouts/partials/head.html`:

```html
<!-- Google Fonts -->
<link
  rel="stylesheet"
  href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700,900"
/>
```

Keep the existing `resources.Get`, `resources.ExecuteAsTemplate`, `toCSS`, `minify`, and `fingerprint` pipeline unchanged.

- [ ] **Step 4: Replace the page shell and primary layout markup**

Change `themes/resume/layouts/index.html` so the body structure is:

```go-html-template
<body>
    <div class="site-shell">
        <article class="resume">
            {{ partial "header.html" . }}
            <hr class="resume-divider">
            {{ if .Site.Params.summary.enable }}
            {{ partial "summary.html" . }}
            {{ end }}
            <hr class="resume-divider">
            <div class="resume-body">
                <main class="resume-main">
                    {{ if .Site.Params.experience.enable }}{{ partial "experience.html" . }}{{ end }}
                    {{ if .Site.Params.projects.enable }}{{ partial "projects.html" . }}{{ end }}
                    {{ if .Site.Params.information.enable }}{{ partial "information.html" . }}{{ end }}
                </main>
                {{ partial "sidebar.html" . }}
            </div>
            {{ if .Site.Params.social.enable }}
            <hr class="resume-divider">
            {{ partial "social.html" . }}
            {{ end }}
        </article>
        {{ partial "footer.html" . }}
    </div>
    {{ partial "scripts.html" . }}
</body>
```

Use the same contract in the primary partials:

```go-html-template
<!-- header.html -->
<header class="resume-header">
    {{ if .Site.Params.profile.enable }}{{ partial "profile.html" . }}{{ end }}
    {{ if .Site.Params.contact.enable }}{{ partial "contact.html" . }}{{ end }}
</header>

<!-- profile.html -->
<div class="resume-identity">
    <h1 class="resume-name">{{ .Site.Params.profile.name }}</h1>
    <div class="resume-tagline">{{ .Site.Params.profile.tagline }}</div>
</div>

<!-- summary.html -->
<section class="resume-intro">
    {{ if .Site.Params.profile.avatar }}
    <img class="resume-profile-image" src="{{ .Site.BaseURL }}assets/images/{{ .Site.Params.profile.avatar }}" alt="{{ .Site.Params.profile.name }}">
    {{ end }}
    <div class="resume-summary">{{ .Site.Params.summary.text | markdownify }}</div>
</section>
```

Keep the original conditions and values exactly; remove only obsolete wrappers, Bootstrap classes, and generated-theme comments.

- [ ] **Step 5: Migrate contact, social, sidebar, and repeated section patterns**

Use this exact list pattern for contact and social links:

```go-html-template
<ul class="resume-contact-list">
    {{ range .Site.Params.contact.list }}
    <li class="resume-contact-item"><i class="resume-contact-icon {{ .icon }}"></i><a class="resume-link" href="{{ .url | safeURL }}">{{ .text }}</a></li>
    {{ end }}
    <li class="resume-contact-item"><i class="resume-contact-icon fas fa-map-marker-alt"></i>{{ .Site.Params.contact.location }}</li>
</ul>
```

```go-html-template
<ul class="resume-social-list">
    {{ range .Site.Params.social.list }}
    <li class="resume-social-item"><a class="resume-link" href="{{ .url }}" target="_blank"><i class="resume-social-icon {{ .icon }}"></i><span>{{ .title }}</span></a></li>
    {{ end }}
</ul>
```

Apply one shared structure to experience, projects, information, education, awards, skills, languages, and interests:

- Section wrappers use `resume-section` plus their existing section-specific class where one exists.
- Every section heading uses only `resume-section-heading`.
- Repeated records use `resume-item`.
- Title and metadata rows use `resume-item-heading`, `resume-item-title`, and `resume-item-meta`.
- Body wrappers use `resume-item-content`; bullet collections use `resume-list`.
- Sidebar and short-form lists use `resume-detail-list` and `resume-detail-item`.
- Secondary values use `muted`.
- `sidebar.html` emits one `aside.resume-aside` and keeps every existing enable condition.
- `footer.html` keeps both attribution lines inside `footer.site-footer`; do not change attribution text or links.

- [ ] **Step 6: Replace `devresume.scss` with the approved standalone stylesheet**

Delete the Bootstrap import and legacy theme rules. Implement these exact tokens and layout values:

```scss
$text-primary: #292929;
$text-secondary: #5d5d5d;
$text-muted: #9e9e9e;
$border: rgba(41, 41, 41, 0.1);
$screen-background: #f6f6f5;
$font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
$mobile-breakpoint: 768px;

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: $screen-background;
  color: $text-secondary;
  font-family: $font-family;
  font-size: 13px;
  font-weight: 400;
  letter-spacing: -0.15px;
  line-height: 1.55;
}

a {
  color: inherit;
}
a:hover {
  color: $text-primary;
}

.site-shell {
  padding: 32px 24px;
}
.resume {
  width: 100%;
  max-width: 1100px;
  margin: 0 auto;
  padding: 48px;
  border: 1px solid $border;
  border-radius: 16px;
  background: #fff;
}
.resume-header {
  display: grid;
  grid-template-columns: minmax(0, 1fr) auto;
  gap: 32px;
  align-items: start;
}
.resume-name {
  margin: 0;
  color: $text-primary;
  font-size: 24px;
  font-weight: 500;
  line-height: 1.15;
}
.resume-tagline {
  margin-top: 8px;
  color: $text-secondary;
  font-size: 14px;
}
.resume-contact {
  font-size: 12px;
}
.resume-contact-list,
.resume-social-list,
.resume-detail-list {
  margin: 0;
  padding: 0;
  list-style: none;
}
.resume-contact-list {
  display: grid;
  gap: 8px;
}
.resume-contact-item {
  display: flex;
  gap: 8px;
  align-items: center;
}
.resume-contact-icon,
.resume-social-icon {
  width: 14px;
  font-size: 14px;
  color: $text-muted;
  text-align: center;
}
.resume-link {
  text-decoration: none;
}
.resume-divider {
  margin: 24px 0;
  border: 0;
  border-top: 1px solid $border;
}
.resume-intro {
  display: flex;
  gap: 16px;
  align-items: center;
}
.resume-profile-image {
  width: 80px;
  height: 80px;
  border-radius: 14px;
  object-fit: cover;
}
.resume-summary {
  font-size: 13px;
}
.resume-summary p,
.resume-item-content p {
  margin: 0;
}
.resume-body {
  display: grid;
  grid-template-columns: minmax(0, 3fr) minmax(180px, 1fr);
  gap: 32px;
}
.resume-aside {
  padding-left: 24px;
  border-left: 1px solid $border;
}
.resume-section + .resume-section {
  margin-top: 24px;
}
.resume-section-heading {
  margin: 0 0 16px;
  color: $text-primary;
  font-size: 13px;
  font-weight: 500;
}
.resume-item {
  break-inside: avoid;
  padding-bottom: 16px;
}
.resume-item + .resume-item {
  padding-top: 16px;
  border-top: 1px solid $border;
}
.resume-item-heading {
  display: flex;
  justify-content: space-between;
  gap: 16px;
  align-items: baseline;
  margin-bottom: 8px;
}
.resume-item-title {
  margin: 0;
  color: $text-primary;
  font-size: 14px;
  font-weight: 500;
}
.resume-item-meta {
  flex: 0 0 auto;
  color: $text-muted;
  font-size: 12px;
  text-align: right;
}
.resume-item-content {
  font-size: 13px;
}
.resume-list {
  margin: 8px 0 0;
  padding-left: 16px;
}
.resume-detail-list {
  display: grid;
  gap: 8px;
  font-size: 12px;
}
.resume-detail-item {
  break-inside: avoid;
}
.muted {
  color: $text-muted;
}
.resume-footer {
  padding-top: 0;
}
.resume-social-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 16px;
  font-size: 12px;
}
.resume-social-item .resume-link {
  display: inline-flex;
  gap: 8px;
  align-items: center;
}
.site-footer {
  width: 100%;
  max-width: 1100px;
  margin: 24px auto 0;
  color: $text-muted;
  font-size: 12px;
  text-align: center;
}
.site-footer p {
  margin: 0;
}

@media (max-width: $mobile-breakpoint - 1px) {
  .site-shell {
    padding: 16px;
  }
  .resume {
    padding: 24px;
  }
  .resume-header,
  .resume-body {
    grid-template-columns: 1fr;
  }
  .resume-header {
    gap: 16px;
  }
  .resume-intro {
    align-items: flex-start;
  }
  .resume-profile-image {
    width: 64px;
    height: 64px;
  }
  .resume-aside {
    padding: 24px 0 0;
    border-top: 1px solid $border;
    border-left: 0;
  }
  .resume-item-heading {
    display: block;
  }
  .resume-item-meta {
    margin-top: 4px;
    text-align: left;
  }
}

@media print {
  body {
    background: #fff;
  }
  .site-shell {
    padding: 0;
  }
  .resume {
    width: 100%;
    max-width: none;
    margin: 0;
    padding: 0;
    border: 0;
    border-radius: 0;
  }
  .resume-body {
    display: block;
  }
  .resume-aside {
    padding: 24px 0 0;
    border-top: 1px solid $border;
    border-left: 0;
  }
  .site-footer {
    margin-top: 24px;
  }
  .resume-item,
  .resume-detail-item {
    break-inside: avoid;
    page-break-inside: avoid;
  }
}
```

Add only selector-specific rules needed by the migrated partials. Do not introduce font sizes, colors, shadows, radii, or breakpoints outside the approved tokens.

- [ ] **Step 7: Run the structural checks to verify the migration is complete**

Run:

```bash
if rg -n 'bootstrap/scss/bootstrap|fonts.googleapis.com|class="[^"]*(container|row|col-|py-|px-|mb-|mr-|ml-|text-|d-|list-|media|rounded|shadow)' themes/resume/assets/scss/devresume.scss themes/resume/layouts; then exit 1; fi
```

Expected: exit 0 with no output.

- [ ] **Step 8: Build and inspect generated output**

Run:

```bash
mise x hugo-extended@0.162.1 -- hugo --minify
rg -n 'resume-name|resume-intro|resume-section-heading|resume-aside|resume-social-list' public/index.html
if rg -n -i '#9580ff|Roboto|bootstrap' public/assets/css; then exit 1; fi
```

Expected: Hugo exits 0 with no warnings; each semantic selector appears in `public/index.html`; the generated CSS contains no purple accent, Roboto, or Bootstrap reference.

- [ ] **Step 9: Inspect responsive and print behavior**

Run `mise x hugo-extended@0.162.1 -- hugo server`, then inspect:

- Desktop at 1440px: 1100px framed document, two-column header, 3:1 body, 80px image, no shadow.
- Mobile at 375px: one-column header and body, metadata below item titles, 64px image, sidebar after main content, no horizontal overflow.
- Browser print preview: white background, no frame or outer margin, one-column body with experience beginning on page 1 and sidebar sections after experience, no entries split when they fit on one page, and all links visible.

Stop the server after inspection.

### Task 2: Remove Vendored Bootstrap Source

**Files:**

- Modify: `config.toml`
- Modify: `themes/resume/exampleSite/config.toml`
- Modify: `themes/resume/theme.toml`
- Modify: `themes/resume/README.md`
- Delete: `themes/resume/assets/scss/bootstrap/`

**Interfaces:**

- Consumes: Task 1's standalone `devresume.scss`, which has no Bootstrap imports.
- Produces: A theme source tree with no unused Bootstrap SCSS, JavaScript, tests, maps, or distribution files.

- [ ] **Step 1: Confirm Bootstrap is unused before deletion**

Run:

```bash
if rg -n -i 'bootstrap' themes/resume/layouts themes/resume/assets/scss/devresume.scss; then exit 1; fi
```

Expected: exit 0 with no output. Do not delete the vendored directory if a runtime reference remains.

- [ ] **Step 2: Replace stale Bootstrap descriptions**

Make these exact text changes while preserving all attribution and license URLs:

```toml
# config.toml [params]
description = "Site Reliability Engineer and DevOps resume for Lucas Saboya"

# themes/resume/exampleSite/config.toml [params]
description = "DevResume - Resume/CV Template For Software Developers"

# themes/resume/theme.toml
description = "DevResume is a resume/CV theme made for software developers."
```

In `themes/resume/README.md`, replace `- Built on Bootstrap 4` with:

```markdown
- Built with semantic HTML and SCSS
```

- [ ] **Step 3: Delete the vendored Bootstrap directory**

Delete `themes/resume/assets/scss/bootstrap/` in full. Do not remove unrelated theme images, layouts, generated resource metadata, or FontAwesome loading.

- [ ] **Step 4: Verify the site still builds without Bootstrap source**

Run:

```bash
mise x hugo-extended@0.162.1 -- hugo --minify
test ! -d themes/resume/assets/scss/bootstrap
if rg -n 'Bootstrap 4|Built on Bootstrap' config.toml themes/resume/exampleSite/config.toml themes/resume/theme.toml themes/resume/README.md; then exit 1; fi
git diff --check
git status --short
```

Expected: Hugo exits 0, the Bootstrap directory is absent, no whitespace errors are reported, and only the approved spec, plan, template, stylesheet, head, and Bootstrap deletion changes appear.
