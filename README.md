# Notes by Nisha

Personal blog of Nisha — a cybersecurity engineer sharing field notes on cloud
security, offensive & defensive tradecraft, and hands-on lab walkthroughs.

Live at **[notesbynisha.com](https://notesbynisha.com)**.

## Stack

- [Hugo](https://gohugo.io/) (extended, `0.147.9`) — static site generator
- [Congo](https://github.com/jpanther/congo) theme (git submodule under `themes/congo`)
- Custom **nisha** colour scheme (Black Panther / Wakanda palette) — dark mode only
- Deployed to GitHub Pages via `.github/workflows/hugo.yml`

## Local development

```bash
# Clone with the theme submodule
git clone --recurse-submodules https://github.com/Nisha318/Nisha318.github.io.git
cd Nisha318.github.io

# If you already cloned without submodules:
git submodule update --init --recursive

# Run the dev server (requires Hugo extended)
hugo server
```

## Structure

| Path | Purpose |
| --- | --- |
| `config/_default/` | Site configuration (params, menus, languages) |
| `content/posts/` | Blog posts |
| `content/about.md` | About page |
| `assets/css/custom.css` | Fonts, gradient bar, homepage styling |
| `assets/css/schemes/nisha.css` | Wakanda colour scheme |
| `layouts/partials/home/custom.html` | Custom homepage (hero + post grid) |
| `layouts/partials/extend-head.html` | Google Fonts |
| `static/assets/` | Images and documents |
| `static/CNAME` | Custom domain (`notesbynisha.com`) |

## Writing a post

Create a Markdown file in `content/posts/` with front matter:

```yaml
---
title: "My Post Title"
date: 2026-07-09
categories: ["Cloud Security"]
tags: ["AWS", "IAM"]
summary: "A one-line summary shown in listings."
showTableOfContents: true
---
```
