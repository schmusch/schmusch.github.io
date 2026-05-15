---
title: "Mensch und KI: Mein Second Brain mit Obsidian"
date: 2026-05-10
draft: false
tags:
  - blogpost
  - obsidian
  - ai
  - ai-systems-design
categories:
  - Tooling
---

![Hero Image](/images/posts/ai-driven-second-brain-hero.jpg)

{{< notice info "Zusammenfassung" >}}
Ich zeige, wie ich meinen Obsidian Vault in ein KI-natives Wissenssystem umgebaut habe. Kernstücke sind die Obsidian CLI für Datensicherheit und ein proaktives Protokoll zur automatischen Vernetzung von Informationen.
{{< /notice >}}

## Warum KIs ein Langzeitgedächtnis brauchen

KIs vergessen alles, sobald man den Tab schließt. Jede Sitzung startet bei null, ohne Kontext zu laufenden Projekten. Wer nach der "Getting Things Done" (GTD) Methode arbeitet, scheitert schnell an dieser Fragmentierung. Notizen manuell zwischen Chat-Fenstern und dem Dateisystem hin- und herzuschieben, kostet zu viel Zeit.

Deshalb nutze ich Obsidian als dauerhaftes Gedächtnis für meine KI-Agenten. Über das integrierte Terminal und die Obsidian CLI steuere ich ein System, das weit über einfache Notizverwaltung hinausgeht. (Das komplette Framework gibt es am Ende des Artikels auf GitHub).

Hier erkläre ich das Setup: Wie standardisierte Befehle, automatisiertes Sortieren und ein Synergie-Protokoll aus einer statischen Ablage einen aktiven Assistenten machen.

## PARA-Struktur als gemeinsamer Nenner

Das PARA-System (Projects, Areas, Resources, Archives) ordnet Informationen für Menschen. Mit standardisierten Metadaten verstehen es auch Maschinen.
```goat
 Schmusch/
   |
   +-- 00-Inbox/
   |
   +-- 10-Projects/
   |
   +-- 20-Areas/
   |
   +-- 30-Resources/
   |
   +-- 40-Archive/
   |
   +-- 90-System/
   |     |
   |     +-- Bases/
   |     |
   |     +-- Templates/
   |     |
   |     +-- Skills/
   |           |
   |           +-- archiving-check/
   |           |
   |           +-- defuddle/
   |           |
   |           +-- json-canvas/
   |           |
   |           +-- obsidian-bases/
   |           |
   |           +-- obsidian-cli/
   |           |
   |           +-- obsidian-markdown/
   |           |
   |           +-- vault-curator/
   |
   +-- CLAUDE.md
   |
   +-- GEMINI.md
```
*Abbildung: Vault-Struktur mit ausgeklapptem `90-System/`-Verzeichnis (Bases, Templates, Skills).*

Die Regeln dafür stehen in der `GEMINI.md` und den jeweiligen Skills. Ein Projekt ist für die KI nicht nur ein Ordner, sondern ein Objekt mit Status, Wichtigkeit und Verlauf in den YAML-Frontmatter-Daten. So behält der Agent den Kontext über Wochen hinweg.

```goat
  1. Interface         2. Instruktionen         3. Middleware           4. Speicherung

 .------------.         .-----------.          .--------------.        .------------.
 | Terminal / |-------->| GEMINI.md |          | Obsidian CLI |------->| PARA Vault |
 | Gemini CLI |         '-----------'          '--------------'        | Markdown & |
 '------------'               |                       ^                | Metadaten  |
                              v                       |                '------------'
                        .-----------.                 |
                        | AI Skills |-----------------'
                        '-----------'
```
*Abbildung: Die vier Schichten der Systemarchitektur.*

Ich steuere den Prozess, die KI pflegt die Struktur und verknüpft die Projekte.

## Git als Synchronisations-Engine

Statt auf Cloud-Dienste setze ich auf GitHub. Das Plugin „Obsidian Git“ committet und pusht im Hintergrund.

Das bringt drei entscheidende Vorteile für KI-Workflows:
- Jede Änderung am Graphen ist versioniert.
- Git löst Synchronisationskonflikte zwischen Geräten sauber auf.
- Die Git-Historie ist das Audit-Log für alle KI-Änderungen.

Zusammen mit dem Terminal entsteht eine Umgebung, in der KI und Texteditor direkt verzahnt sind.

## Die Inbox als zentraler Trichter

Neue Gedanken müssen sofort und ohne Reibung ins System. Früher nutzte ich Daily Notes, heute wandert alles Unstrukturierte direkt in den Ordner `00-Inbox`. 

![Obsidian Terminal Split](/images/posts/obsidian-terminal-split.png)
*Abbildung: Das Obsidian-Interface mit integriertem Terminal als Steuerzentrale.*

Die KI hat Zugriff auf alle Projekte. Sie scannt die Inbox, extrahiert Aufgaben, sortiert Web-Clippings als Ressourcen ein und legt sie am richtigen Ort ab. Die Erfassung ist damit nicht das Ende, sondern der Trigger für die automatisierte Sortierung.

## Das Betriebssystem: GEMINI.md und Skills

Die `GEMINI.md` ist das Gesetzbuch des Vaults. Sie setzt die PARA-Struktur durch, ähnlich wie im LLM Wiki von Andrej Karpathy, wo der Vault die primäre Wissensbasis für das Sprachmodell bildet.

Die Ausführung übernehmen modulare "Skills" (basierend auf Kepanos Obsidian Skills, aber für den Headless-Betrieb umgeschrieben).

Das Setup ist aktuell auf die Gemini CLI zugeschnitten, aber die Architektur ist portabel. KI-Agenten wie Claude Code können die Instruktionen in der `GEMINI.md` und den `SKILL.md`-Dateien einfach einlesen und auf ihre eigene Syntax anpassen.

## Metadaten und JSON Canvas

Die KI organisiert den Vault über Metadaten. Da jede neue Notiz getaggt wird, baue ich dynamische Dashboards, die Projekte nach Wichtigkeit statt nach Alter sortieren. So fallen wichtige, aber vernachlässigte Projekte sofort auf.

Für Architekturskizzen oder Beziehungs-Graphen bearbeitet die KI JSON Canvas Dateien. Sie zeichnet Knoten und Kanten, um Zusammenhänge visuell darzustellen.

## Die Obsidian CLI schützt den Graphen

Dateien einfach per Terminal (`mv` oder `sed`) zu verschieben, zerstört eingehende Wikilinks.

Deshalb ist die Obsidian CLI zwingende Middleware. Wir nutzen die internen Obsidian-Routinen statt des Betriebssystems:

```bash
# Anti-Pattern: Zerstört Links
mv "00-Inbox/Notiz.md" "10-Projects/Projekt_A/"

# Best-Practice: Aktualisiert den Graphen
obsidian move path="00-Inbox/Notiz.md" newPath="10-Projects/Projekt_A/"
```

Das sichert den Index. Auch Metadaten bearbeitet die KI nur über die CLI (`obsidian property:set`), damit kein fehlerhaftes YAML den Frontmatter zerschießt.

## Das Synergie-Protokoll

Jede Interaktion mit der KI endet mit Datenpflege:
- Sie schreibt kurze Zusammenfassungen der bearbeiteten Notizen.
- Sie schlägt interne Links vor, um das Wissen im Graphen zu verankern.
- Sie fragt, ob Änderungen im Projekt-Logbuch notiert werden sollen.

Mit jeder Interaktion wird der Vault wertvoller.

## Negative Search für die Wartung

Ein wachsender Vault vermüllt. Projekte bleiben als "aktiv" liegen, Metadaten fehlen. Hunderte Dateien manuell oder per KI zu prüfen, frisst Zeit und Token.

Die Lösung ist "Negative Search". Statt alle Dateien ins Context-Window zu laden, sucht die KI über die CLI nach Lücken:

```bash
# Finde alle Notizen ohne das Pflichtfeld "type:"
obsidian search query="-\"type:\" OR -\"created:\""
```

Aus diesen Treffern baut der "Vault Curator"-Skill einen Diagnosebericht mit fertigen Reparaturbefehlen. Ich muss nur noch bestätigen.

## Fazit

Dieses Setup spart mir täglich Zeit. Der Vault ist keine statische Ablage mehr, sondern ein dynamisches System, das mitdenkt.

{{< notice tip "Framework auf GitHub" >}}
Das komplette Framework mit GEMINI.md und allen Skills liegt öffentlich auf GitHub: **[github.com/schmusch/ai-driven-second-brain](https://github.com/schmusch/ai-driven-second-brain)**. MIT-Lizenz, klonen und anpassen erlaubt.
{{< /notice >}}
