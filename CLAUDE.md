# CLAUDE.md

This file provides guidance for AI assistants working in this repository.

## Project Overview

**kseki.github.io** is a Japanese technical blog ("Klog") built with [Hugo](https://gohugo.io/), a Go-based static site generator. The live site is published at https://kseki.github.io/.

- **Language:** Japanese (ja-jp)
- **Theme:** [Hugo Stack v3](https://github.com/CaiJimmy/hugo-theme-stack) (loaded via Hugo modules)
- **Hosting:** GitHub Pages (deployed from `gh-pages` branch)

---

## Repository Structure

```
kseki.github.io/
├── .github/workflows/       # CI/CD: deployment and linting
├── archetypes/default.md    # New post template
├── assets/scss/custom.scss  # Custom SCSS overrides
├── config/_default/         # Hugo configuration (TOML)
│   ├── config.toml          # Base URL, language, pagination
│   ├── params.toml          # Site parameters (sidebar, widgets, comments)
│   ├── markup.toml          # Markdown rendering (Goldmark, syntax highlight)
│   ├── menu.toml            # Navigation and social links
│   ├── module.toml          # Hugo modules (theme import)
│   ├── permalinks.toml      # URL structure
│   └── related.toml         # Related posts algorithm
├── config/production/
│   └── services.toml        # Google Analytics (production only)
├── content/
│   ├── page/                # Static pages (about, archives, search, links)
│   └── post/                # Blog posts (one directory per post)
├── layouts/
│   ├── _default/_markup/    # Custom Markdown render hooks (Mermaid)
│   ├── partials/            # Template partials (head, footer)
│   └── shortcodes/          # Custom Hugo shortcodes
├── static/                  # Favicons, PWA manifest, static assets
├── go.mod / go.sum          # Hugo Go module dependencies
├── package.json             # Node.js dev dependencies (textlint only)
└── .textlintrc.json         # Japanese text linting rules
```

---

## Development Workflows

### Prerequisites

- **Hugo** (extended version, latest) — required for SCSS compilation
- **Go** 1.17+ — required for Hugo module resolution
- **Node.js / npm** — required for text linting

### Local Development

```bash
# Install Node dependencies (for linting)
npm install

# Start local dev server with live reload
hugo server

# Start with drafts visible
hugo server -D
```

The local server runs at http://localhost:1313/ by default.

### Creating a New Blog Post

Use Hugo's archetype to scaffold a new post:

```bash
hugo new post/<slug>/index.md
```

This creates a file from `archetypes/default.md` with the standard frontmatter template. Place any post-specific images in the same directory as the `index.md` file (page bundle pattern).

### Building for Production

```bash
hugo --minify --gc --logLevel debug
```

Output goes to the `public/` directory. This directory is in `.gitignore` and is not committed.

---

## Content Conventions

### Blog Post Frontmatter

All posts in `content/post/<slug>/index.md` use YAML frontmatter:

```yaml
---
title: title
description: description
keywords:
    - XXX
slug:
date: YYYY-MM-DDTHH:MM:SS+09:00
image:
categories:
    - XXX
tags:
    - XXX
weight: 1
draft: false
---
```

- **`slug`** — used in the URL: `/posts/<slug>/`
- **`image`** — cover image filename (place in same directory as `index.md`)
- **`draft: false`** — set to `true` to hide from production
- **`categories`** and **`tags`** — used for taxonomy pages and the tag cloud widget

### Standard Post Sections (Japanese)

The archetype defines these standard H3 sections (in Japanese):

| Section | Meaning |
|---------|---------|
| `### 概要` | Overview / Summary |
| `### 設定方法` | Configuration method |
| `### 実行` | Execution / Running |
| `### まとめ` | Conclusion / Wrap-up |
| `### 参考` | References |

Use these conventions when writing new posts.

### Mermaid Diagrams

Mermaid diagrams are rendered via a custom code block hook. Use a fenced code block with the `mermaid` language identifier:

````markdown
```mermaid
graph TD
    A --> B
```
````

### Amazon Product Cards

Use the custom shortcode to embed Amazon affiliate product cards:

```
{{< product_card_amazon asin="ASIN_HERE" >}}
```

The affiliate ID (`kseki0c-22`) is configured in `config/_default/params.toml`.

---

## Text Linting

The blog uses **textlint** with Japanese technical writing rules. Always run lint before committing content changes.

```bash
# Lint and auto-fix all posts
npm run lint
```

This runs `textlint --fix 'content/post/**/*.md'`.

### Linting Rules (`.textlintrc.json`)

- **Preset:** `textlint-rule-preset-ja-technical-writing` with these overrides:
  - Full-width `！` and `？` are allowed
  - Doubled particles: not strict; `か` is allowed
  - Spaces around code/inline code required
  - Period mixing: `:` is allowed as a period mark
  - Sentence length check: **disabled**
- **Filter:** TOML frontmatter blocks (between `+++`) are whitelisted

---

## CI/CD

### GitHub Actions Workflows

| Workflow | Trigger | Purpose |
|----------|---------|---------|
| `.github/workflows/gh-pages.yml` | Push to `main` | Build Hugo site and deploy to `gh-pages` branch |
| `.github/workflows/reviewdog.yml` | Pull requests | Run textlint and report results as PR checks/reviews |

### Deployment Flow

1. Push changes to `main`
2. GitHub Actions builds with `hugo --minify --gc --logLevel debug`
3. Built `public/` is force-pushed (single commit) to the `gh-pages` branch
4. GitHub Pages serves the `gh-pages` branch at https://kseki.github.io/

---

## Site Features & Configuration

| Feature | Config Location | Notes |
|---------|----------------|-------|
| Comments | `config/_default/params.toml` | Disqus (`k-s-blog`) |
| Google Analytics | `config/production/services.toml` | Production only (`G-6K4G66HC3S`) |
| Color scheme | `config/_default/params.toml` | Auto dark/light toggle |
| Amazon affiliate | `config/_default/params.toml` | JP affiliate ID `kseki0c-22` |
| Reading time | `config/_default/params.toml` | Enabled |
| Content license | `config/_default/params.toml` | CC BY-NC-SA 4.0 |

### Homepage Widgets (in order)

1. Search
2. Archives (limit: 5)
3. Categories (limit: 10)
4. Tag cloud (limit: 10)

### Page-level Widgets

- Table of Contents (ToC)

---

## Key Conventions for AI Assistants

1. **Language:** Blog content is written in Japanese. When writing or editing post content, use Japanese.
2. **One post per directory:** Each post lives in `content/post/<slug>/index.md`. Images and assets go in the same directory.
3. **Run lint after editing posts:** Always run `npm run lint` after modifying any Markdown files in `content/post/`.
4. **Don't commit `public/`:** The build output directory is gitignored and managed by CI/CD.
5. **Theme is a Go module:** Do not modify theme files directly. Customizations go in `layouts/`, `assets/scss/custom.scss`, or `static/`.
6. **Config format is TOML:** All Hugo configuration files use TOML syntax.
7. **No README.md:** This project uses CLAUDE.md as the primary documentation for contributors and AI assistants.
