---
title: "Mein neues Portfolio: Setup mit Hugo und GitHub Pages"
date: 2026-05-02
draft: false
tags: ["hugo", "github-pages", "obsidian", "ci-cd"]
---

# Eine neue Portfolioseite

Um meine Projekte besser präsentieren zu können, habe ich mich entschieden, eine statische Webseite aufzubauen. Mein Ziel war ein schnelles, sicheres und leicht zu wartendes Setup, das sich nahtlos in meinen bestehenden Workflow integriert.

## Der Tech-Stack

Die Wahl fiel auf **Hugo**, einen extrem schnellen Static Site Generator, geschrieben in Go. Als Theme nutze ich das minimalistische **Coder Theme**.

Die wichtigsten Komponenten:
- **Hugo:** Generiert die statischen HTML-Seiten aus Markdown.
- **GitHub Pages:** Übernimmt das kostenlose Hosting.
- **GitHub Actions:** Eine CI/CD-Pipeline, die bei jedem Push automatisch die Seite neu baut und veröffentlicht.
- **Obsidian:** Mein primärer Editor. Ich schreibe alle Inhalte direkt in meinem lokalen Vault, da das Hugo-Projektverzeichnis vollständig integriert ist.

## Der Workflow

Dank der Integration in Obsidian ist das Bloggen und Pflegen der Seite sehr reibungslos:

1.  **Schreiben:** Ich erstelle eine neue Markdown-Datei in Obsidian (`site/content/posts/`). Dank Obsidian-Templates ist der YAML-Frontmatter sofort korrekt formatiert.
2.  **Vorschau:** Bei Bedarf starte ich lokal `hugo server -D` und sehe Änderungen in Echtzeit.
3.  **Deploy:** Ein einfaches `git push` reicht. GitHub Actions übernimmt den Rest. In wenigen Minuten ist der neue Inhalt online.

## Architektur-Visualisierung

Hier ist eine vereinfachte Darstellung meines Veröffentlichungs-Workflows:

{{<mermaid>}}
%%{init: {'theme': 'neutral'}}%%
graph TD
    A[Obsidian Vault] -->|git push| B(GitHub Repository)
    B --> C{GitHub Actions}
    C -->|Build: hugo| D[Public HTML]
    D --> E((GitHub Pages))
{{</mermaid>}}

## Warum dieser Setup?

Es gibt keine Datenbank, keine komplizierten Plugins und keine Sicherheitsprobleme wie bei klassischen CMS (z.B. WordPress). Der gesamte Content ist versioniert, gehört komplett mir (als Markdown-Dateien) und die Auslieferung über GitHub Pages ist global und extrem performant.

*Dieses Setup wurde im Rahmen eines initialen Projekts vollständig dokumentiert und automatisiert.*
