# Yair Shenfeld’s website

A Jekyll site with four pages, structured publication/talk data, and local templates. Content and public URLs are independent of the folder structure, so the design can change without moving the content or breaking links.

## Local development

Use Ruby with Bundler. The committed `Gemfile.lock` records the resolved dependency versions; keep it in version control.

```sh
bundle install
bundle exec ruby scripts/check_content.rb
bundle exec jekyll serve
```

Open http://localhost:4000. Restart the server after changing `_config.yml`. To generate the complete site:

```sh
JEKYLL_ENV=production bundle exec jekyll build
```

The build writes `_site/`. Never edit that directory directly. `_site/`, Jekyll caches, and `.DS_Store` files are ignored by Git.

The content checker validates data, renders HTML through Jekyll, checks duplicate output paths and internal link targets, and repeats the checks with a `/preview` base URL. It does not compile Sass, contact external links, or replace a browser check. For layout changes, check desktop and mobile widths, keyboard navigation, and the menu around 600px after building locally.

## Where things live

| Location | Purpose |
| --- | --- |
| `_pages/index.md` | Homepage text, portrait, and contact links; published at `/` |
| `_pages/flows.md` | Course description and notes link |
| `_pages/papers.md`, `_pages/talks.md` | Page metadata and loops that render the data |
| `_data/papers.yml` | Ordered publication records |
| `_data/talks.yml` | Ordered talk sections and records |
| `_layouts/default.html` | Shared HTML shell |
| `_includes/head.html`, `_includes/header.html` | Metadata, stylesheet loading, and navigation |
| `_includes/paper.html`, `_includes/talk.html` | Reusable record templates |
| `_includes/resource-links.html` | Shared rendering for labeled resource links |
| `assets/css/custom.css` | Site settings, shared components, and page-specific styles |
| `assets/img/` | Images |
| `_config.yml` | Site identity, theme, navigation, page defaults, and exclusions |
| `CNAME` | Custom domain for GitHub Pages |
| `scripts/check_content.rb` | Content, template, and route checks |

## Editing publications and talks

Entries appear in the same order as their YAML lists. Add a publication at the desired position in `_data/papers.yml`:

```yaml
- title: "Example paper: a subtitle"
  url: https://example.com/paper
  authors:
    - Yair Shenfeld
    - Another Author
  author_order: non-alphabetical
  citation: "*Journal Name*, volume, pages (year)."
  links:
    - label: arXiv
      url: https://arxiv.org/abs/example
    - label: code
      url: https://github.com/example/project
```

`author_order` can be `alphabetical` or `non-alphabetical`. Omit it for entries without a marker, such as single-author papers. The template places the marker beside the name configured as `author.name` in `_config.yml`. Author names and titles are plain text; `citation` accepts inline Markdown, such as italics. Keep citations to one paragraph. Quote YAML strings containing a colon followed by a space, or beginning with `*`.

Talks are grouped under sections in `_data/talks.yml`:

```yaml
- title: Research Talks
  talks:
    - title: "Example talk"
      links:
        - label: Slides
          url: https://example.com/slides.pdf
        - label: Video
          url: https://example.com/video
```

Add sections or resource links as needed; the templates render them automatically. To add new fields such as a year, abstract, or thumbnail, add them to the YAML records, update the relevant include, and extend the checker if those fields need validation. Use a Jekyll collection instead if publications later need substantial individual pages.

## Adding pages and layouts

Create a Markdown file in `_pages` with front matter:

```yaml
---
title: Teaching
permalink: /teaching/
class: teaching-page
---
```

Write content below the closing `---`. The default layout comes from `_config.yml`; a page can override it with `layout: another-layout`. Add an entry to `navigation` in `_config.yml` if the page should appear in the menu. Navigation order follows that list.

Keep existing `permalink` values when renaming or moving source files. `_pages` is included explicitly and is not a collection. The homepage belongs here too, with `permalink: /`. No root-level index file is required.

For a different page structure, create `_layouts/another-layout.html`. It can inherit the site shell with front matter `layout: default` and wrap `{{ content }}` with additional markup. Shared components belong in `_includes`.

Internal links and image paths in Liquid templates should use `relative_url`:

```liquid
<a href="{{ '/teaching/' | relative_url }}">Teaching</a>
```

This keeps links working if the site is later hosted under a path prefix. Set `url` to the origin and `baseurl` to the prefix (or an empty string for a root-domain site).

## Changing the design

The site owns its layouts and includes. Minima 2.5 supplies the base typography, spacing, stylesheet at `/assets/main.css`, and the optional Google Analytics include. Its gem dependencies also supply the SEO and feed plugins. The local head explicitly uses the SEO tag; the site does not advertise a blog feed in its head.

Start with the CSS variables at the top of `assets/css/custom.css` for colors and content widths. The remaining sections cover shared layout, navigation, homepage, papers, talks, and responsive behavior. Navigation uses its own classes instead of Minima’s menu classes. Its toggle and link visibility share one 600px breakpoint. Page-specific rules should be scoped to the page’s body class and `.page-content` so they do not inadvertently alter the header.

The wave decoration is controlled by `decoration: wave` on a navigation entry and `header_decoration: wave` in page front matter. Its SVG color is embedded in `--wave-image`; update that as well when changing the accent color.

For a major redesign, replace the shared layout, includes, and stylesheet while retaining the page front matter and data. If removing Minima, first provide a replacement base stylesheet and update `_includes/head.html`. Declare `jekyll-seo-tag` directly in `Gemfile` if retaining `{% seo %}` without Minima, and replace or remove the optional `google-analytics.html` include. Update `Gemfile.lock` with Bundler after dependency changes.

## Deployment

The configured public origin is `https://yairshenfeld.com`; `CNAME` contains the custom domain. The Git remote is a GitHub Pages repository. No deployment workflow is committed here, so the actual publishing method is controlled outside these files in the repository’s Pages settings. This reorganization does not change those settings or deploy the site.

For a future custom build workflow, use the committed Ruby dependencies to build with `JEKYLL_ENV=production` and publish the generated `_site/` directory. Check the repository’s existing Pages configuration before changing how it publishes. `README.md`, dependency manifests, and `scripts/` are excluded from the generated site.
