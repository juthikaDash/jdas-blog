# jdas-blog

Personal research & technical blog of **Juthika** — multi-agent LLM systems, AI inference, and system design.

🌐 **Live site:** https://juthikadash.github.io/jdas-blog/

Built with [Jekyll](https://jekyllrb.com/) and the [al-folio](https://github.com/alshedivat/al-folio) theme, hosted on [GitHub Pages](https://pages.github.com/).

---

## ✍️ What's here
- **Projects** — write-ups on TradeDeck (AI-powered trading dashboard) and Ctrl-Alt-Feel (multi-agent emotion engine)
- **Research notes** — energy-aware inference scheduling for multi-agent LLM pipelines
- **Engineering posts** — system design, AI inference, and lessons from building with LLMs

## 🛠️ Run locally

**Prerequisites:** Ruby 3.x and Bundler

```bash
git clone https://github.com/juthikaDash/jdas-blog.git
cd jdas-blog
gem install bundler
bundle install
bundle exec jekyll serve
```

Then open **http://localhost:4000/jdas-blog/** (the `/jdas-blog/` path matches `baseurl` in `_config.yml`).

Live-reload while editing:

```bash
bundle exec jekyll serve --livereload
```

## 📝 Write a new post

Create a file in `_posts/` named `YYYY-MM-DD-title.md`:

```markdown
---
layout: post
title: "Building TradeDeck"
date: 2026-10-06
description: How I built an AI-powered trading dashboard
tags: ai llm full-stack
categories: projects
---

Your content here, in Markdown.
```

## 📁 Structure

| Path | Purpose |
|---|---|
| `_config.yml` | Site settings (`url`, `baseurl`, name, socials) |
| `_posts/` | Blog posts |
| `_pages/` | About, projects, CV pages |
| `_projects/` | Project cards |
| `assets/` | Images, PDFs, CSS, JS |

## 🚀 Deployment

Pushing to `main` triggers the **Deploy site** GitHub Action, which builds the site and publishes it to the `gh-pages` branch. GitHub Pages serves from `gh-pages` → `/ (root)`.

Key `_config.yml` settings for this project site:

```yaml
url: https://juthikadash.github.io
baseurl: /jdas-blog
```

## 📄 License

Theme: [al-folio](https://github.com/alshedivat/al-folio) (MIT). Content © Juthika.
