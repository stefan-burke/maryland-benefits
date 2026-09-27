# MarylandBenefits.org

The public-facing site for Maryland's benefits application — a static
two-page artifact keeping the concluded program live: a landing page that
hands off to myMDTHINK, and the privacy policy. Built from
[CfA Static](https://github.com/codeforamerica/cfa-static), Code for
America's template for small informational sites; template updates reach
this site only through a reviewed `upstream` merge.

## What this site is

- **`/` (from `src/pages/home.md`)** — the application card: a welcome
  heading, the myMDTHINK hand-off button, and the Code for America service
  notice.
- **`/privacy/` (from `src/pages/privacy.md`)** — the full privacy policy,
  with an anchored in-page table of contents built from its headings.

Content is the snapshot captured from the existing site; the visual design
mirrors the original black-header / white-card Honeycrisp skin. Both live in
the frontmatter `blocks:` of those two files.

## How the site departs from template defaults

- **Collections**: pages and snippets only. News, guides, search, the
  `/blocks/` gallery, the theme editor, and the `/theme-editor/` page are
  removed; CMS features are limited accordingly (`cms_config` in
  `src/_data/site.json`).
- **Theme**: `src/css/theme.scss` carries the site palette (black header and
  primary buttons, `#f5f5f5` body, Maryland blue `#003865` links, white card)
  plus the handful of component rules the tokens cannot express.
- **Includes customised**: `src/_includes/navigation-start.html` renders the
  MarylandBenefits.org wordmark in the header; `src/_includes/footer.html`
  renders the Code for America wordmark next to the
  `footer-content` snippet.
- **Logo assets**: `src/images/wordmark.svg`, `maryland-cta.svg`, and
  `cfa-logo-white.svg`.
- **Config**: `src/_data/config.json` turns off breadcrumbs, the theme
  switcher, and horizontal nav; `blockLayouts.json` gives pages a
  contents-then-content two-column layout.

## Working on it

- [`CLAUDE.md`](CLAUDE.md) — engineering policy and workflow
- [`docs/developer-reference.md`](docs/developer-reference.md) — commands and CMS use
- [Block reference](skills/cfa-static-site-builder/references/blocks.md) — generated block schemas
- [`BLOCKS_LAYOUT.md`](BLOCKS_LAYOUT.md) — reference navigation

## Checks

```bash
npm run build        # builds _site/ and checks internal links
npm run check:a11y   # axe-core WCAG 2.2 AA over every built page
npm test             # full unit and quality-gate suite
npm run lint         # Biome over JS
```

All quality gates pass. Eight tests in three files fail, each pinned to a
deliberate site decision rather than a defect:

- `test/unit/collections/navigation.test.js` (3) — this site has no search
  page, so the nav search form is not rendered; the tests render it whenever
  `src/pages/search.md` exists.
- `test/unit/utils/pages-yml-block-sync.test.js` (2) — no guide or news
  content exists, so the guide block components in `.pages.yml` are
  unreferenced by any page.
- `test/integration/eleventy/seo-metadata.test.js` (3) — breadcrumbs are
  disabled (the original site has none) and the test hardcodes the template
  demo identity (`publisher.name: "CfA Static"`).

`src/images/party.jpg`, `src/images/menu.jpg`, and
`src/files/template-overview.txt` are not referenced by any published page;
they exist because the template's integration tests build fixture sites from
the fork's own sources. The build's unused-image report also names
`cfa-logo-white.svg` and `maryland-cta.svg`, which are used — through the
header/footer includes and a hero block — but that scanner only reads
markdown page bodies, and this site's pages are block-only.

Manual keyboard and screen-reader review is still required before
acceptance; automation covers structure, contrast, and labels only.

## Deployment

The deployable artifact is `_site/` — no application server, no secrets.
Publishing is owned by CfA infrastructure; this repo's workflows are the
template's default SharedServices and GitHub Pages pipelines, and CfA
decides which serves the production domain. The site's canonical URL is
`https://www.marylandbenefits.org`.
