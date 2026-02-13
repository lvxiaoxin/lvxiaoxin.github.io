# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jekyll-based personal blog hosted on GitHub Pages at https://lvxiaoxin.github.io. The blog is bilingual (Chinese/English) covering topics like programming, books, life reflections, and technical tutorials.

## Development Commands

```bash
# Install dependencies
bundle install

# Serve site locally (default: http://localhost:4000)
bundle exec jekyll serve

# Build site for production
bundle exec jekyll build

# Serve with drafts visible
bundle exec jekyll serve --drafts
```

## Architecture & Structure

### Content Organization

- **`_posts/<category>/YYYY-MM-DD-title.md`**: Published blog posts, organized by category subdirectories
  - Categories: Architecture, Book, CodeTraining, Coursera, Docker, GraphQL, JavaScript, Kubernetes, Python, blog
  - Post filename format is strict: date prefix required (YYYY-MM-DD-title.md)
- **`_drafts/`**: Unpublished draft posts (no date prefix needed)
- **`pages/`**: Static pages (about.md, links.md, categories.md, etc.)
- **`_data/`**: YAML data files
  - `links.yml`: Friend links for the links page
  - `skills.yml`: Skills keywords displayed on about page
  - `social.yml`: Social media links

### Templates & Components

- **`_layouts/`**: Page layouts
  - `default.html`: Base layout with header/footer
  - `post.html`: Blog post layout (includes comments, social sharing, sidebar)
  - `page.html`: Static page layout
  - `categories.html`: Category listing page
  - `wiki.html`: Wiki-style page layout
- **`_includes/`**: Reusable components
  - Sidebar components: search, post navigation, popular repos, QR code, categories nav
  - `comments.html`: Gitalk comment integration
  - `sns-share.html`: Social sharing buttons
  - `header.html`, `footer.html`: Site header and footer

### Post Frontmatter Template

All posts must include frontmatter:

```yaml
---
layout: post
title: Post Title Here
categories: [Category1, Category2]  # or single: CategoryName
description: Brief description for SEO
keywords: keyword1, keyword2
---
```

### Configuration

- **`_config.yml`**: Main Jekyll configuration
  - Site metadata (title, description, author)
  - Navigation menu structure
  - Gitalk comment system settings
  - Google Analytics
  - Jekyll plugins and build settings
  - Pagination set to 10 posts per page

## Deployment

- Deployment is automatic via GitHub Pages
- Push to `master` branch triggers rebuild
- No manual build/deploy commands needed
- Site URL: https://lvxiaoxin.github.io

## Important Notes

- The blog uses kramdown for Markdown parsing with GFM input
- Posts support both Chinese and English content
- Category names are case-sensitive and used in URLs
- Images for posts are typically stored in `_posts/<category>/pic/` subdirectories or in `images/` for site-wide assets
- The site uses Gitalk for comments, which requires GitHub OAuth app configuration
