---
title: "Mein neues Portfolio: Setup mit Hugo und GitHub Pages"
date: 2026-05-02
draft: false
tags:
  - hugo
  - github-pages
  - obsidian
  - ci-cd
  - static-site-generator
categories:
  - Tooling
premise: Ein wartungsarmes Portfolio-Setup durch die Integration von Obsidian, Hugo und GitHub Actions.
angle: Efficiency
---
![Hugo Portfolio Setup Architektur](/images/portfolio-setup.png)

Mein Portfolio nutzt einen git-basierten Workflow. Er verbindet Obsidian als Headless-CMS mit Hugo und GitHub Pages zu einer statischen Seite, die ihre Inhalte direkt aus meinem Knowledge-Management-System bezieht.

## Der Tech-Stack

Das Setup nutzt vier Kernkomponenten. [Hugo](https://gohugo.io) kompiliert die Markdown-Dateien in statisches HTML, während GitHub Pages die generierten Assets hostet. GitHub Actions baut das Projekt automatisch bei jedem Push auf den `main`-Branch. [Obsidian](https://obsidian.md) dient als lokaler Editor, da das Hugo-Repository direkt im Obsidian-Vault liegt.

Für das Styling nutze ich das [Coder-Theme](https://github.com/luizdepra/hugo-coder), ergänzt durch lokales `custom.scss`. Diagramme rendere ich nativ über GoAT direkt aus ASCII-Art.

## Der Workflow

Die Content-Erstellung passiert in Obsidian. Neue Beiträge erstelle ich über das `obsidian-cli`, damit der YAML-Frontmatter sofort korrekt formatiert ist. Der Befehl `obsidian create path="site/content/posts/neuer-beitrag.md" template="Hugo-Blogpost"` legt den Artikel an. Ein lokaler `hugo server -D` rendert Drafts als Vorschau. Ein Commit und Push triggert dann das finale Deploy via GitHub Action.

Hier ist der Workflow visuell:

```goat
  1. Schreiben          2. Versionieren         3. Build                 4. Hosting

 .------------.        .--------------.        .----------------.       .--------------.
 | Obsidian   |------->| GitHub Repo  |------->| GitHub Actions |------>| GitHub Pages |
 | Vault      |        | (git push)   |        | (hugo build)   |       | (CDN/SSL)    |
 '------------'        '--------------'        '----------------'       '--------------'
```
*Abbildung: Vom Markdown im Vault bis zum statischen Asset auf GitHub Pages.*

## Automatisierung via CI/CD

Die Pipeline ist minimalistisch. Die GitHub Action checkt das Repo aus, installiert Hugo und deployt die Artefakte auf GitHub Pages.

```yaml
# .github/workflows/hugo.yml
name: Deploy Hugo site to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.128.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
        run: |
          hugo --minify --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v5
```

Dieses Setup garantiert volle Datenkontrolle. Der Content liegt lokal als Markdown vor, die Auslieferung skaliert über das GitHub CDN.