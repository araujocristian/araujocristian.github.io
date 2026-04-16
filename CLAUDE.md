# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Quartz v4 digital garden — a static site generator that publishes Obsidian markdown notes as a website. Published at **araujocristian.github.io**. Content is written in Portuguese and English.

## Commands

```bash
npm ci                        # Install dependencies (Node >= 22, npm >= 10.9.2)
npx quartz build              # Build static site to public/
npx quartz build --serve      # Build + local dev server with hot reload
npx quartz build --concurrency=1  # Build with single worker (debugging)
npm run check                 # TypeScript type check + Prettier format check
npm run format                # Auto-format with Prettier
npm run test                  # Run tests (tsx --test)
```

To speed up builds during development, comment out `Plugin.CustomOgImages()` in `quartz.config.ts` (noted in the file).

## Architecture

Quartz uses a **parse → transform → filter → emit** pipeline:

- **`quartz.config.ts`** — Main config: site metadata, plugin pipeline, theme colors/fonts
- **`quartz.layout.ts`** — Page layout: component placement for content pages and list pages
- **`content/`** — Markdown source files (Obsidian vault). Frontmatter uses `title`, `draft`, `tags`
- **`public/`** — Generated output (git-ignored in CI, committed locally for sync)
- **`quartz/`** — Core framework (forked from jackyzha0/quartz)
  - `plugins/transformers/` — Markdown processing (frontmatter, syntax highlighting, LaTeX, wikilinks)
  - `plugins/filters/` — Content filtering (draft removal)
  - `plugins/emitters/` — Output generators (HTML pages, RSS, sitemap, OG images)
  - `components/` — Preact components (Search, Graph, Explorer, Backlinks, TOC, etc.)
  - `cli/` — CLI handlers for build/create/sync commands
  - `build.ts` — Build orchestration with watch mode

## Key Config Choices

- **SPA mode** enabled (client-side navigation)
- **Obsidian-flavored markdown** with wikilinks (`shortest` link resolution)
- **LaTeX** via KaTeX
- **Analytics**: Plausible
- **Ignored patterns**: `private`, `templates`, `.obsidian`
- **Draft filtering**: files with `draft: true` in frontmatter are excluded from build

## Deployment

Pushes to the **v4** branch trigger GitHub Actions (`.github/workflows/deploy.yml`) which builds and deploys to GitHub Pages. The v4 branch is both the main branch and the deploy branch.

## Working with Content

Content files live in `content/` and follow Obsidian conventions. Internal links use `[[wikilink]]` syntax. The `CreatedModifiedDate` plugin reads dates from frontmatter first, then git history, then filesystem.
