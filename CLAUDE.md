# OFERA Content Studio — Projektübersicht für Claude

## Projektziel & Kontext

OFERA (ofera.at) ist ein österreichisches Unternehmen für Aquaponik und Insektenzucht. Das Shopify-Theme ist **Impulse V8 von Archetype**. Shopifys eingebauter Editor für Blog-Artikel, Produktbeschreibungen und Collection-Bodies ist unbrauchbar für reich formatierte Inhalte. Daher dieses Tool.

**Kernziel:** Stefan kann im Browser Content schreiben (Markdown + Shortcodes, wie in Obsidian), live previewen, und sauberes HTML exportieren das in Shopify eingefügt wird. Für Blog-Artikel, Produktbeschreibungen und Collection-Bodies.

**Prinzipien:**
- Stabile, einfache Browser-App — keine unnötigen Features, keine fragile Komplexität
- Iterative Weiterentwicklung: neue Komponenten und Styling-Fixes ohne strukturelle Brüche
- Single Source of Truth: `ofera-components.css` im Studio und als Shopify-Asset
- Stefan arbeitet gern in Markdown/Obsidian → das ist der primäre Input-Workflow

**Export-Workflow:**
- Einziger Export: **„Export HTML for Shopify"** → schlankes HTML ohne Inline-Styles, CSS kommt aus Shopify-Asset `ofera-components.css`
- Kein HTML+CSS Export mehr (wurde entfernt, da Shopify `<style>`-Blöcke aus Artikel-Inhalten strippt und der Button damit nutzlos war). Auch im gehosteten HTTPS-Setup bleibt HTML-only der richtige Exportpfad.

**Shopify CSS-Einbindung:**
```liquid
{{ 'ofera-components.css' | asset_url | stylesheet_tag }}
```
Jeweils ganz oben in der Section-Datei einfügen (nicht in `layout/theme.liquid` — führt zu Theme-Override-Konflikten):
- `sections/article-template.liquid` ✅ bereits live eingebunden
- `sections/main-collection.liquid` — noch einfügen (collection.description → Zeile 42, `<div class="rte collection__description">`)
- `sections/main-product.liquid` — noch einfügen (Beschreibung kommt aus Snippet `product-description.liquid`, CSS-Tag trotzdem in main-product.liquid)

**Hosting-Status:** Das Studio ist inzwischen in ein GitHub-Repository eingecheckt und läuft über HTTPS. Hinweise, die noch von reinem `file://`-Betrieb ausgehen, sollten als veraltet behandelt werden.

## Was ist das?

Eine **Single-File Browser-App** (`content-studio.html`) zum Erstellen von reich formatierten Shopify-Blogartikeln für OFERA (österreichisches Aquaponik-/Insektenzucht-Unternehmen).

Kein Framework, kein dediziertes Backend. Der aktuelle Standardbetrieb läuft gehostet über HTTPS; lokales Öffnen der HTML-Datei ist optional weiterhin möglich.

## Architektur

```
content-studio.html          — Haupt-App (Editor-UI, Parser, Modals, alles drin)
ofera-components.css         — Komponenten-CSS für Shopify (wird auch im Studio eingebunden)
content/                     — Artikel und Referenzen als .txt (Shortcode-Syntax)
  _komponenten-referenz.txt  — Alle Komponenten als Beispiel (zum Importieren)
```

## Layout-Struktur (HTML)

```
body (flex-column)
  .studio-header          — Logo, Aktions-Buttons
  .meta-panel             — aufklappbares SEO-Panel
  .studio-toolbar         — Format- und Komponenten-Buttons (2 Zeilen)
  .studio-main (flex-row)
    .pane-editor
      .pane-header
      .editor-gutter-wrap   ← flex:1, display:flex — wichtig!
        .editor-gutter      ← Shortcode-Marker-Streifen
        textarea#editor
    .pane-preview
      .pane-header
      .preview-body
        .article-preview
```

**Wichtig:** `.editor-gutter-wrap` muss `flex: 1; display: flex; min-height: 0; overflow: hidden` haben, sonst schrumpft die Textarea auf eine minimale Größe (bekannter Bug, bereits gefixt).

## Shortcode-System

Markdown + eigene Shortcodes werden client-seitig geparst und in HTML umgewandelt. Reihenfolge entspricht der Toolbar und `_komponenten-referenz.txt`.

| # | Shortcode | Beschreibung |
|---|-----------|-------------|
| 01 | `[toc]` oder `[toc title="..." depth="2\|3"]` | Inhaltsverzeichnis (auto aus ##-Headings) |
| 02 | `[callout color="green\|orange\|blue\|gray" title="..."]...[/callout]` | Hinweisbox, `title=` optional. Legacy: `[tipp]`, `[wichtig]`, `[info]` |
| 03 | `[inline-teaser type="shop\|digital" ...]` | Kompakter Produkt-/Kurslink |
| 04 | `[product-card ...]...[/product-card]` | Vollständige Produktkarte |
| 05 | `[product-grid cols="2\|3"]...[/product-grid]` | Product Cards nebeneinander |
| 06 | `[pricing-table cols="2\|3" type="shop\|digital"]...[/pricing-table]` | Preistabelle, optionales `image=` pro Tier |
| 07 | `[image src="..." size="full\|medium\|small"]` | Responsive Bild, `title=` und `caption=` optional |
| 08 | `[image-row cols="2\|3"]...[/image-row]` | Bilder nebeneinander, `title=` und `caption=` pro Bild optional |
| 09 | `[video url="..."]` | YouTube-Embed (in der Studio-Preview als Platzhalter, im Export als echtes Embed) |
| 10 | `[editorial type="shop\|digital" ...]...[/editorial]` | Narrative Karte, Body Text (Markdown) optional zwischen Tags |
| 11 | `\| Header \| ...` oder `[table row-headers]\|...\|[/table]` | Markdown-Tabellen, Responsive-Stacking auf Mobile. `row-headers` macht erste Spalte grün (für Matrix-Ansichten) |

## Edit-Button System (`_reg`)

Jede Komponente in der Preview hat einen „✏ Bearbeiten"-Button. Dieser wird über `_reg(type, sc, html)` registriert. Beim Klick lädt `editComponent(idx)` → `prefill*()` → Modal mit vorausgefüllten Feldern. `_commitSC()` schreibt zurück in den Editor. Beim frischen Öffnen eines Modals (kein Edit-Modus) werden alle Felder automatisch geleert.

`finishEdit()` verwendet nth-occurrence-Erkennung: wenn zwei identische Shortcodes im Editor existieren, wird beim Bearbeiten des zweiten nicht fälschlicherweise der erste ersetzt.

## Farbpalette

- OFERA Grün: `#094B20`
- Digital/Dunkel: `#001817`
- Hintergrund grün: `#f0f5f1`
- Border: `#e4e4e4`, `#e2e8f0`
- Editor-Hintergrund: `#1e1e2e` (Catppuccin Mocha)

## CSS-Besonderheiten

- **Keine CSS-Variablen in `ofera-components.css`** — Farben hardcoded für Shopify-Kompatibilität (Impulse Theme)
- CSS-Variablen nur in der Studio-UI (`:root` in `content-studio.html`)
- **`!important` auf Link-Styles** — überschreibt Impulse-Theme-Regel `.rte a:not(.btn)`
- **`!important` auf `.ofera-table th { background, color }`** — Impulse Theme setzt global `td, th { background: var(--colorBody) }`. Da `background` nicht vererbt wird, muss es direkt auf `th` gesetzt werden, nicht auf `thead tr`.
- **`.ofera-table--row-headers tbody td:first-child { background, color }` mit `!important`** — aus dem gleichen Grund (Theme-Reset auf `td`)
- **Video-Struktur**: `[video]` rendert `<div class="ofera-video-block">` als äußeren Container. `<div class="ofera-video">` enthält nur den iframe. Title/Caption (`.om-title`, `.om-cap`) sind Geschwister-Elemente von `.ofera-video` innerhalb `.ofera-video-block` — nicht Kinder. Grund: der Cookie-Consent-Manager (CMP) auf der Shopify-Seite manipuliert `.ofera-video` (setzt overflow:hidden/Höhe), was Kindelemente unsichtbar macht. Außerhalb des `.ofera-video` sind sie davon unberührt.
- Mobile-First, Breakpoints: 768px, 640px, 560px
- `border-radius: 10px` als Standard für alle Karten/Boxen (Callout, TOC, Tabelle, Pricing Card, Image, Image-Row)

## Impulse Theme — Wichtige Erkenntnisse

- Artikel-Body-Wrapper: `<div class="article__body rte">` (doppelter Unterstrich `__`)
- Globale Theme-Resets: `td, th { background: var(--colorBody); padding: 10px 15px; }` — muss mit `!important` auf Komponenten-Ebene überschrieben werden
- `.rte > div { margin-bottom: 15px }` — gilt für direkte Kind-Divs des RTE-Containers
- `.rte table { table-layout: fixed }` — keine weiteren störenden Tabellen-Styles
- CMP (Cookie-Consent-Manager) manipuliert YouTube-iframe-Container via JavaScript

## Workflow

1. Artikel in `.txt` schreiben (Shortcode-Syntax)
2. Studio rendert Live-Preview rechts
3. Meta & SEO Panel für Keyword-Optimierung
4. **„Export HTML for Shopify"** klicken → HTML in Zwischenablage
5. In Shopify-Artikel einfügen (HTML-Editor)
6. CSS-Asset `ofera-components.css` in Shopify hochladen wenn sich CSS geändert hat

## Bekannte Bugs & Offene Punkte

- **YouTube-Embed in der Preview als Platzhalter**: Das Studio zeigt absichtlich eine Vorschau mit Video-ID statt eines Live-iframes. Der Export rendert das echte YouTube-Embed für Shopify.
- **Editor schrumpft auf kleinen Streifen** (gefixt): Fix: `flex: 1; display: flex; min-height: 0; overflow: hidden` auf `.editor-gutter-wrap`.
