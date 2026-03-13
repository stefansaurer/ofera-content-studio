# OFERA Content Studio

Lokales Tool zum Schreiben und Exportieren von reich formatierten Inhalten für Shopify — Blogartikel, Produktbeschreibungen und Collection-Bodies.

Kein Server, kein Framework. Alles läuft lokal im Browser.

---

## Einmalige Einrichtung (Shopify)

1. In Shopify: **Online Store → Themes → Edit code → Assets**
2. Neue Datei `ofera-components.css` anlegen
3. Inhalt von `ofera-components.css` aus diesem Ordner hineinkopieren
4. In `sections/article-template.liquid` ganz oben einfügen:
   ```liquid
   {{ 'ofera-components.css' | asset_url | stylesheet_tag }}
   ```

Für Produktbeschreibungen und Collections: gleiche CSS-Zeile in `sections/main-product.liquid` bzw. `sections/main-collection.liquid` einfügen.

---

## Täglicher Workflow

### 1. Studio öffnen
`content-studio.html` im Browser öffnen (Doppelklick reicht).

### 2. Artikel schreiben
- Direkt im Editor tippen, oder
- Bestehende `.txt`-Datei über **Importieren** laden
- Rechts erscheint die Live-Vorschau
- Komponenten über die Toolbar einfügen (Buttons oben)
- Jede Komponente in der Vorschau hat einen **✏ Bearbeiten**-Button

### 3. SEO & Meta ausfüllen
**Meta & SEO**-Panel aufklappen → Titel, Beschreibung, URL-Handle, Fokus-Keyword eintragen.

### 4. Als .txt speichern
**Speichern**-Button → Datei landet im Download-Ordner → in `content/` ablegen.
Die `.txt` ist deine SSOT — hier wird weitergearbeitet, nicht im exportierten HTML.

### 5. Für Shopify exportieren
**Export HTML for Shopify** → HTML ist in der Zwischenablage.

### 6. In Shopify einfügen
Artikel/Produkt/Collection öffnen → HTML-Editor (`<>`) → einfügen → speichern.

### 7. CSS-Update (nur wenn ofera-components.css geändert wurde)
Shopify → Assets → `ofera-components.css` → Inhalt ersetzen → speichern.

---

## Artikel-Syntax

Normaler Fließtext wird als Markdown geschrieben. Komponenten als Shortcodes.

### Textformatierung
```
**fett**   *kursiv*   ~~durchgestrichen~~   [Linktext](url)
```

### Komponenten-Übersicht

| Shortcode | Beschreibung |
|-----------|-------------|
| `[toc]` | Inhaltsverzeichnis (auto aus ##-Überschriften) |
| `[tipp]...[/tipp]` | Grüne Hinweisbox |
| `[wichtig]...[/wichtig]` | Orange Warnbox |
| `[info]...[/info]` | Blaue Infobox |
| `[callout color="gray" title="..."]...[/callout]` | Neutrale Box mit optionalem Titel |
| `[inline-teaser type="shop\|digital" ...]` | Kompakter Produkt-/Kurslink im Fließtext |
| `[product-card ...]...[/product-card]` | Vollständige Produktkarte |
| `[product-grid cols="2\|3"]...[/product-grid]` | Mehrere Produktkarten nebeneinander |
| `[pricing-table cols="2\|3" type="shop\|digital"]...[/pricing-table]` | Preistabelle |
| `[image src="..." size="full\|medium\|small"]` | Einzelbild |
| `[image-row cols="2\|3"]...[/image-row]` | Bilder nebeneinander |
| `[video url="..."]` | YouTube-Embed |
| `[editorial type="shop\|digital" ...]...[/editorial]` | Narrative Empfehlungskarte |
| `\| Spalte 1 \| Spalte 2 \|` | Markdown-Tabelle |

Alle Beispiele zum Importieren: `content/_komponenten-referenz.txt`

---

## Artikel übersetzen (z.B. mit ChatGPT)

`.txt`-Inhalt kopieren und mit folgendem Prompt übersetzen:

> Übersetze diesen Artikel ins Englische. Behalte alle Shortcodes exakt bei. Übersetze nur menschlich lesbaren Text — also Werte bei `title=`, `caption=`, `cta=`, `badge=`, `label=`, `sub=`, `alt=`, `price-note=`, `social-proof=`, `tier=` und Fließtext. Lass alle `url=`, `src=`, `image=` und `price=` Werte unverändert.

Danach: übersetzte `.txt` in `content/` speichern (z.B. `artikel-en.txt`), `url=`-Werte auf englische Produkt-Handles prüfen.

---

## Dateistruktur

```
content-studio.html          — Die App (im Browser öffnen)
ofera-components.css         — CSS für alle Komponenten (Shopify-Asset)
content/
  _komponenten-referenz.txt  — Alle Komponenten als Beispiel
  dein-artikel.txt           — Deine Artikel (SSOT)
shopify-theme-impulse-v8/    — Theme-Referenz (nur zum Nachschlagen)
```
