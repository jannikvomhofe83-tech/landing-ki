# Flowstate AI Solutions — Landingpage „AI Readiness Check“

Konversionsoptimierte Landingpage für Meta-Ads-Traffic → vorqualifizierter Lead → 60-Minuten-Analysegespräch.
Zielgruppe: **Wirtschaftskanzleien** (Wirtschaftsrecht/B2B) — ohne Größen­einschränkung.

Umgesetzt **1:1 nach** `01_LANDINGPAGE_INHALT_STRUKTUR` (Inhalt/Copy/Formular/Recht) und
`02_LANDINGPAGE_DESIGN` (Silent-Luxury-Hybrid, WCAG-Farbmatrix, Typografie, Komponenten).

---

## Sofort ansehen

Doppelklick auf **`index.html`** — die Seite läuft ohne Build-Schritt, ohne Server, ohne Abhängigkeiten.
(Für 100 % korrektes Font-Rendering am besten über einen lokalen Server, z. B. `npx serve .` oder VS-Code „Live Server“.)

## Design-Theme

**Durchgängig dunkel** (Schwarz/Surface + Gold-Akzent, weißer Text) — auf Kundenwunsch abweichend vom ursprünglichen Hybrid des Design-Dokuments. Umgesetzt als sauberes Dark-Theme über Token-Redefinition, **nicht** durch Übermalen: alle WCAG-Kontraste bleiben AAA (Weiß auf Schwarz 21:1, Gold auf Schwarz 9,19:1), Karten heben sich per Fläche/Rahmen ab, Formularfelder sind vertieft, Gold bleibt reiner Akzent (≤ ~10 %). Auf Schwarz ist Gold-Text WCAG-konform — die „kein Gold-Text auf Hell"-Regel greift, weil es keine hellen Flächen mehr gibt.
> Hinweis aus dem Design-Dokument: Der Hybrid war u. a. mit der älteren Zielgruppe (Ø 53,7 J.) begründet. Die Lesbarkeit bleibt dank durchgängig hoher Kontraste (weißer Text, ≥ 17 px) gewahrt — bei Bedarf lässt sich das Theme über die 5 Farb-Tokens am Ende des `<style>`-Blocks wieder umstellen.

## Technischer Ansatz

- **Ein einziges, selbsttragendes HTML-File** (`index.html`) mit inline-CSS (bester LCP, kein Render-Blocking) und schlankem Vanilla-JS. Kein Framework, kein Tailwind-Runtime, kein Build.
- **Inter self-hosted** als WOFF2 (`fonts/`) — **kein Google-Fonts-CDN** (DSGVO, LG-München).
- **Video als Facade-Pattern**: Poster lädt sofort, der Player erst nach Klick → kein Autoplay, kein Cookie vor Consent, bester LCP.
- **Meta-Pixel lädt ausschließlich nach aktiver Einwilligung** (§ 25 TDDDG).
- Alle Design-Tokens aus dem Design-Dokument sind als CSS-Variablen umgesetzt (Farben, Typo-Skala, Spacing, Radien, Schatten).

## Dateien

```
index.html          → die komplette Landingpage (14 Sektionen, Formular, Consent)
impressum.html      → Impressum-Vorlage (§ 5 DDG)
datenschutz.html    → Datenschutz-Vorlage (Art. 13 DSGVO, inkl. Meta-Pixel-Abschnitt)
favicon.svg         → Favicon (Marken-Monogramm)
robots.txt          → Suchmaschinen
fonts/              → Inter (self-hosted, WOFF2)
media/poster.svg    → Platzhalter-Videoposter (ersetzen, siehe unten)
media/logo-mark.png → Logo-Zeichen (freigestellt aus Bilder/Logo.jpeg)
media/logo-word.png → Schriftzug „Flowstate AI" (freigestellt)
Bilder/Logo.jpeg    → Original-Logo (Quelle für den Freisteller)
```

**Header:** Sticky-Header mit freigestelltem Logo links (transparent, weiß auf Schwarz) und CTA-Button rechts (→ Formular). Der Header ersetzt den früheren unteren Sticky-CTA-Balken. Neues Logo? Einfach `Bilder/Logo.jpeg` austauschen und den Freisteller neu erzeugen (weißes Logo auf dunklem Grund → Alpha aus Helligkeit).

---

## ⚠️ Vor dem Livegang ausfüllen

Diese Stellen sind bewusst als Platzhalter angelegt und müssen befüllt werden:

| # | Was | Wo |
|---|-----|-----|
| 1 | **n8n-Webhook** für die Lead-Übergabe | `index.html`, JS-Konstante `FORM_ENDPOINT` (aktuell leer = Demo-Modus, Formular zeigt Erfolg, sendet aber nicht) |
| 2 | **Meta-Pixel-ID** | `index.html`, JS-Konstante `META_PIXEL_ID` (leer = Pixel wird nie geladen) |
| 3 | **Video** von Lukas | `#videoFrame` → Attribut `data-video-src` (self-hosted `.mp4` **oder** Cloudflare-Stream/Vimeo-Embed-URL). Untertitel Pflicht. |
| 4 | **Videoposter** (Standbild Lukas) | `media/poster.svg` durch AVIF/WebP-Foto ersetzen und `src` im `<img>` anpassen (`fetchpriority="high"` behalten) |
| 5 | **Impressum-Daten** | `impressum.html` — alle `[Platzhalter]` (Anschrift, Telefon, ggf. USt-IdNr.) |
| 6 | **Datenschutz-Daten** | `datenschutz.html` — `[Platzhalter]` (Anschrift, Hosting-Anbieter, eingesetzte Dienste, Stand) |
| 7 | **Domain / Canonical / OG-Image** | `index.html` `<head>`: `canonical`, `og:image` (1200×630) und Domain in `robots.txt`/`datenschutz`-Links |
| 8 | **Kontakt-E-Mail** | überall `kontakt@flowstate-ai.de` prüfen/ersetzen |

> Die Rechtstexte sind **standardkonforme Vorlagen** und ersetzen keine anwaltliche/DSB-Prüfung. Vor Veröffentlichung prüfen lassen. Abschnitte zu Diensten (Meta-Pixel, Video-Hosting, E-Mail, CRM) nur behalten, wenn tatsächlich eingesetzt.

### Formular-Backend anbinden (Beispiel)

`FORM_ENDPOINT` auf den n8n-Webhook setzen. Das Formular sendet ein JSON:

```json
{ "kanzleigroesse":"31–45","rolle":"Partner / Geschäftsführer","zeitverlust":"...",
  "zeitpunkt":"Jetzt","firmenname":"...","name":"...","email":"...","telefon":"...",
  "einwilligung":true,"seite":"ki-effizienz-analyse","zeitstempel":"2026-…Z" }
```
n8n → CRM + Brevo-Bestätigungsmail (bestehender Flowstate-Stack).
Mandatstyp **„Überwiegend Privatmandate"** bekommt automatisch eine freundliche Bestätigung ohne Terminangebot — der Lead wird trotzdem übergeben (Aussteuerung nach Fit, nicht nach Größe).

**Statistik-Quellen (Stand der Recherche):** McKinsey *The State of AI 2025* (Headline 88 %/7 %), Thomson Reuters *Future of Professionals 2025* (190 Std./Anwalt·Jahr), MIT NANDA *GenAI Divide 2025* (95 %), Wolters Kluwer *Future Ready Lawyer* 2026/2024 (92 % / 73 %), Atlassian *State of Teams 2025* (~25 %), DMB *Risiko-Report Mittelstand 2026* (54,7 %), BRAK (Rückgang niedergelassener Anwälte; KI-Handlungshinweise 12/2024), Bitkom 2025. Nicht-DE- bzw. nicht-anwaltsspezifische Zahlen sind auf der Seite als solche gekennzeichnet.

---

## Deployment (Netlify)

Statische Seite — einfach das gesamte Verzeichnis deployen:

- **Drag & Drop:** Ordner auf https://app.netlify.com/drop ziehen. Fertig.
- **Oder Git/CLI:** `netlify deploy --prod` (kein Build-Command nötig, Publish-Verzeichnis = Projektordner).

Consent-Tool (Cookiebot/Usercentrics) optional zusätzlich einbinden — der eingebaute Banner erfüllt § 25 TDDDG bereits (Meta-Pixel erst nach Opt-in, „Ablehnen“ gleichwertig sichtbar).

---

## Rechts-Checkliste (Doc 1, §16) — Status

- [x] Impressum nach **§ 5 DDG** (nicht TMG), im Footer, ≤ 2 Klicks erreichbar
- [x] Datenschutzerklärung nach **Art. 13 DSGVO**, getrennt vom Impressum
- [x] Cookie-Consent nach **§ 25 TDDDG** — „Ablehnen“ gleichwertig sichtbar, kein Dark Pattern
- [x] **Meta-Pixel lädt erst nach aktivem Opt-in** (in Demo aus, bis ID gesetzt)
- [x] **Video ohne Cookie-Setzung vor Consent** (Facade, Player erst bei Klick)
- [x] Formular: Datenminimierung, Zweckbindung, Einwilligung + Widerrufshinweis
- [x] **Kein ODR-Link**

## Design-Checkliste (Doc 2, §12) — Status

- [x] **Kein Gold-Text auf hellem Grund** (Gold auf Hell nur als gefüllte Fläche / dekoratives Icon)
- [x] Video zentriert, kein Autoplay, Poster als LCP-Bild (`fetchpriority="high"`), Untertitel vorgesehen
- [x] Alle Buttons: **Gold-Fläche + schwarzer Text** (9,19 : 1, AAA)
- [x] Focus-States auf allen interaktiven Elementen (3 px Outline)
- [x] Formular einspaltig, Labels sichtbar, `type`/`inputmode`/`autocomplete` gesetzt, Touch-Targets ≥ 44 px, `font-size ≥ 16px` (kein iOS-Zoom)
- [x] Impressum (§ 5 DDG) + Datenschutz verlinkt, kein ODR-Link
- [x] WCAG-Kontraste laut Matrix (Text-Kombinationen AAA)
- [x] `prefers-reduced-motion` respektiert, kein Zähler-Hochzählen, kein Parallax
- [x] Statistik-Zahlen `ink`, nicht Gold; Gold-Akzent nur über dünne Linie/Icon
- [x] 110-Stunden-Rechenbeispiel mit sichtbarer Warn-Akzentbox (kein graues Kleingedrucktes)
- [ ] Lighthouse Mobil ≥ 90 / LCP ≤ 2,5 s — **nach Einbindung von echtem Video/Poster final messen**
- [ ] „ISO 27001“/„DSGVO-zertifiziert“-Badge — bewusst **nicht** verwendet (nur vorsichtige Formulierungen)

## Inhaltliche Guardrails (Doc 1, §18) — eingehalten

Keine fixe ROI-/Einsparungs-Garantie · nicht „vollständiger Audit“ für den kostenlosen Check · kein „DSGVO/ISO-zertifiziert“ · keine Vollautomatisierungs-Versprechen · keine fixe Onboarding-Verkürzung · 110-Std.-Rechnung nur mit Illustrationshinweis · keine erfundenen Logos/Testimonials.

---

## QA (bereits automatisiert geprüft)

- Rendering Desktop (1440 px) & Mobil (390 px) — kein horizontales Scrollen, alle 14 Sektionen korrekt.
- Formular: Leer-Validierung (8 Pflichtfelder), In-Scope-Erfolg, Out-of-Scope-Sonderfall, Consent-Pflicht.
- Cookie-Banner erscheint bei Erstbesuch; Pixel lädt nicht vor Consent; Accept/Reject werden gespeichert.
- FAQ-Accordion, Video-Facade, Sticky-Mobile-CTA, Scroll-Reveal (mit Sicherheitsnetz).
