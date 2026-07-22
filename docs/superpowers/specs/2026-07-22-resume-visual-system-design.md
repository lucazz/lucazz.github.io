# Resume Visual System Design

## Goal

Restyle the existing single-page resume using the reference's restrained typography, monochrome hierarchy, spacing, and framing without turning the page into an application interface.

## Scope

- Preserve all resume copy, Hugo configuration keys, section enablement, section order, and theme attribution.
- Preserve every partial, including currently disabled education, awards, projects, and information sections.
- Replace Bootstrap-dependent markup in all layouts with semantic resume classes.
- Remove Bootstrap and the hosted Roboto request. Keep Hugo Pipes and FontAwesome.
- Replace Hugo's deprecated `languageCode` configuration and `.Site.LanguageCode` template access with `locale` and `.Site.Language.Locale` in the root site and vendored example site.
- Replace stale root, example-site, theme, and README descriptions that claim the site uses Bootstrap; preserve attribution and license URLs.

## Visual System

- Use `-apple-system`, `BlinkMacSystemFont`, "Segoe UI", and `sans-serif`. Apple devices render native SF Pro; no font files are downloaded.
- Use only 400 and 500 font weights with `-0.15px` letter spacing.
- Limit type sizes to 12px, 13px, 14px, and 24px:
  - 24px for the profile name.
  - 14px for the tagline and item titles.
  - 13px for section headings, summary text, body copy, and list items.
  - 12px for contact details, metadata, sidebar details, social links, and footer copy.
- Use `#292929` for primary text, `#5D5D5D` for supporting text, and `#9E9E9E` for metadata. Use `rgba(41, 41, 41, 0.1)` borders and `#F6F6F5` for the screen background; remove the purple accent.
- Use an 8px spacing base with 16px, 24px, and 32px multiples for section and page spacing.
- Use a 16px radius for the screen-only resume frame, a 14px radius for the profile image, and no decorative shadow.
- Size contact and social icons at 14px.

## Layout

- Keep the resume as one centered document with a maximum width of 1100px.
- Use a two-column header with identity on the left and contact details on the right.
- Keep the profile image beside the summary at 80px on desktop and 64px on mobile.
- Use a 3:1 main-content/sidebar grid separated by a low-contrast rule.
- Render experience and other repeated entries as a continuous list separated by rules, not cards.
- Keep social links in a centered footer row within the resume document.
- Below 768px, stack the header, summary, item metadata, and body into one column. Move sidebar sections after the main content and replace the vertical divider with a horizontal rule.

## Template Cleanup

- Replace Bootstrap grid, spacing, display, typography, list, media, rounding, and shadow utilities with semantic classes owned by `devresume.scss`.
- Use the same section, item, metadata, list, and muted-text classes across enabled and disabled partials.
- Keep Hugo conditionals and ranges unchanged so configuration continues to control rendering.
- Change the profile image's generic `alt="image"` text to the configured profile name.
- Do not change external link behavior, resume content, or FontAwesome icon configuration beyond sizing.

## Print

- Remove the page background, outer margin, frame border, and radius when printing.
- Use a single-column print layout so long experience content fragments cleanly across pages; place sidebar sections after the main content.
- Avoid page breaks inside individual experience, project, education, award, and information entries.
- Keep all content and links visible in print.

## Verification

- Run `hugo --minify` with Hugo Extended and require a successful build with no deprecation warnings.
- Confirm layouts contain no Bootstrap utility classes and `devresume.scss` contains no Bootstrap import.
- Confirm `head.html` no longer requests Roboto.
- Confirm current enabled sections render in generated HTML and disabled partials use the same semantic class system.
- Inspect desktop, mobile, and print layouts for hierarchy, overflow, stacking, and readable page breaks.
