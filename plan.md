# waza-notes — GitHub Pages Site Plan

## Goal

A static site deployed to GitHub Pages to track feedback and notes from karate trainings, organized in a nested category structure.

## Tech Stack

- **Jekyll** — static site generator (native GitHub Pages support)
- **Dependencies** — `github-pages` gem (locks Jekyll + plugins to GitHub Pages versions)
- **Theme** — built-in [hacker](https://github.com/pages-themes/hacker) (`theme: jekyll-theme-hacker`)
- **Deployment** — GitHub Actions

> The Hacker theme has no sidebar navigation out of the box.
> Override `_layouts/default.html` (copied from the theme repo) and add `_data/navigation.yml` + `_includes/sidebar.html` to add one.
> Override styles via `/assets/css/style.scss` importing `{{ site.theme }}`.

---

## Repository Structure

```
waza-notes/
├── .github/
│   └── workflows/
│       └── deploy.yml          ← GitHub Actions build & deploy
├── _data/
│   └── navigation.yml          ← nested nav structure
├── _layouts/
│   └── default.html            ← copied from pages-themes/hacker, customized for sidebar
├── _includes/
│   └── sidebar.html            ← renders navigation.yml recursively
├── assets/
│   └── css/
│       └── style.scss          ← imports theme, adds custom CSS
├── _config.yml
├── Gemfile
├── .gitignore                  ← ignores Gemfile.lock
├── index.md
├── general.md
├── dictionary.md
├── geri-waza/
│   ├── index.md
│   ├── gin-geri.md
│   ├── mae-geri.md
│   ├── mawashi-geri.md
│   ├── kansetsu-geri.md
│   ├── yoko-geri.md
│   └── ushiro-geri.md
└── kata/
    ├── index.md
    ├── pinan-sono-ichi.md
    ├── pinan-sono-ni.md
    ├── pinan-sono-san.md
    ├── pinan-sono-yon.md
    ├── pinan-sono-gp.md
    ├── tsukino-kata.md
    └── saifa.md
```

---

## Page Structure

Each page is a Markdown file with front matter:

```yaml
---
title: Gin Geri
---
```

No special parent/child front matter needed — navigation hierarchy is defined centrally in `_data/navigation.yml`.

---

## Navigation Data (`_data/navigation.yml`)

```yaml
- title: General
  url: /general
- title: Dictionary
  url: /dictionary
- title: Geri Waza
  url: /geri-waza
  children:
    - title: Gin Geri
      url: /geri-waza/gin-geri
    - title: Mae Geri
      url: /geri-waza/mae-geri
- title: Kata
  url: /kata
  children:
    - title: Pinan Sono Ichi
      url: /kata/pinan-sono-ichi
```

---

## Jekyll Config (`_config.yml`)

```yaml
title: waza-notes
theme: jekyll-theme-hacker
permalink: pretty
# For project sites served at user.github.io/waza-notes/
baseurl: "/waza-notes"
url: "https://rhsplinter.github.io"
```

---

## Gemfile

Generated via `jekyll new --skip-bundle .`, then edited to use the `github-pages` gem:

```ruby
source "https://rubygems.org"

# gem "jekyll", "~> 4.x"   ← commented out; github-pages pins Jekyll
gem "github-pages", "~> GITHUB-PAGES-VERSION", group: :jekyll_plugins
```

> Replace `GITHUB-PAGES-VERSION` with the latest from <https://pages.github.com/versions.json>.

---

## Custom Styles (`assets/css/style.scss`)

```scss
---
---

@import "{{ site.theme }}";

/* custom overrides below */
```

---

## `.gitignore`

```
Gemfile.lock
_site/
.jekyll-cache/
```

---

## GitHub Actions Workflow (`.github/workflows/deploy.yml`)

```yaml
name: Deploy Jekyll to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: ruby/setup-ruby@v1
        with:
          ruby-version: '3.3'
          bundler-cache: true
      - id: pages
        uses: actions/configure-pages@v5
      - run: bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
      - uses: actions/upload-pages-artifact@v3
        with:
          path: _site

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

---

## GitHub Repo Settings

1. Go to **Settings → Pages**
2. Set source to **GitHub Actions**

---

## Adding New Content

To add a new category or note:
1. Create a `.md` file in the appropriate folder
2. Add front matter with `title:`
3. Add the entry to `_data/navigation.yml`
4. Commit and push — GitHub Actions deploys automatically

---

## Implementation Steps

1. Create GitHub repo `waza-notes`
2. Scaffold locally: `jekyll new --skip-bundle .`
3. Edit `Gemfile` → use `github-pages` gem; run `bundle install`
4. Add `Gemfile.lock` to `.gitignore`
5. Set `theme: jekyll-theme-hacker` (+ `baseurl`/`url`) in `_config.yml`
6. Add `.github/workflows/deploy.yml`
7. Add `_data/navigation.yml`
8. Copy `_layouts/default.html` from [pages-themes/hacker](https://github.com/pages-themes/hacker); customize + add `_includes/sidebar.html`
9. Add `assets/css/style.scss` importing `{{ site.theme }}`
10. Add initial content pages
11. In **Settings → Pages**, set source to **GitHub Actions**
12. Push to `main` — first deploy runs automatically
