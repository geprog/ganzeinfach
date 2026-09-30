# CLAUDE.md

Kontext und Checkliste für Claude Code beim Arbeiten an dieser Seite. Ziel: eine moderne, schnelle, barrierefreie und für KI-Systeme (Suchmaschinen-LLMs, Crawler, Screenreader) gut lesbare Seite — ganz ohne Tracking und ohne fremde Server, siehe [README.md](README.md).

## Vor jedem Commit / vor "fertig" prüfen

### 1. Farbkontrast (WCAG AA)

Bei jeder Farb- oder Textänderung die Kontraste neu rechnen, nicht nur "sieht gut aus" einschätzen:

- Normaler Text: **mindestens 4,5:1** gegen seinen Hintergrund.
- Großer Text (≥24px normal oder ≥18,66px fett): **mindestens 3:1**.
- Nicht-Text-UI (Button-Umrandungen, Fokus-Ring, Icon-Konturen): **mindestens 3:1**.
- Bei `opacity` auf Text zählt die tatsächlich gerenderte (gemischte) Farbe, nicht der reine Farbwert.

Schnelltest per Python (Werte anpassen):

```bash
python3 -c "
def lin(c):
    c=c/255
    return c/12.92 if c<=0.03928 else ((c+0.055)/1.055)**2.4
def lum(h):
    h=h.lstrip('#'); r,g,b=int(h[0:2],16),int(h[2:4],16),int(h[4:6],16)
    return 0.2126*lin(r)+0.7152*lin(g)+0.0722*lin(b)
def contrast(a,b):
    la,lb=lum(a),lum(b); hi,lo=max(la,lb),min(la,lb)
    return (hi+0.05)/(lo+0.05)
print(contrast('#121826','#5C8B7A'))
"
```

Aktuell bekannte knappe Stelle: Button-Text `--night` auf `--brand-teal` liegt bei ~4,58:1 (knapp über der 4,5-Grenze). Diesen Wert beim Ändern von `--brand-teal` oder `--night` neu prüfen, nicht heller/entsättigter machen ohne Nachrechnen.

Falls ein echter Lighthouse-Lauf gewünscht ist (Node/npx nötig, nicht immer verfügbar):

```bash
npx --yes lighthouse file:///pfad/zu/index.html --only-categories=accessibility,seo,performance,best-practices --view
```

### 2. Barrierefreiheit sonst

- Landmark-Struktur einhalten: `header`/`nav`/`main`/`section`/`footer` (wie bisher).
- Überschriften-Hierarchie ohne Sprünge: ein `h1` pro Seite, danach `h2`, `h3` fortlaufend.
- Dekorative SVGs/Icons mit `aria-hidden="true" focusable="false"`, echte Bildinhalte mit sinnvollem `alt`.
- Sichtbarer Fokus-Zustand für alle interaktiven Elemente (`:focus-visible`, nicht per `outline:none` entfernen).
- Interaktive Elemente per Tastatur erreichbar und in sinnvoller Reihenfolge (kein `tabindex` > 0).
- Farbe niemals als einziges Unterscheidungsmerkmal (z. B. "Bald da"-Kacheln haben zusätzlich Text/Badge, nicht nur eine andere Farbe).

### 3. Performance

- Keine externen Schriften, Skripte, Analytics oder CDN-Assets laden — Grundprinzip der Seite (siehe README, "ohne Tracking"). Alles bleibt im Repo.
- Icons/Grafiken als Inline-SVG statt Rasterbildern, wo möglich (kleiner, schärfer, kein extra Request).
- CSS/JS klein halten und in der Seite selbst bündeln (kein Render-Blocking durch externe Dateien).
- `prefers-reduced-motion` respektieren (bereits vorhanden — bei neuen Animationen mit einbeziehen).
- Bilder mit Breite/Höhe bzw. definiertem Seitenverhältnis einbinden, damit kein Layout-Sprung (CLS) entsteht.

### 4. SEO & KI-Lesbarkeit

- `<title>` und `<meta name="description">` pro Seite spezifisch setzen (nicht generisch kopieren).
- `lang`-Attribut korrekt (`de`).
- Statisches HTML ohne Client-seitiges Rendering für Kerninhalte — Crawler und LLMs sehen den vollen Inhalt ohne JS ausführen zu müssen. So beibehalten, keine SPA-Frameworks für diese Seiten einführen.
- Sprechende Linktexte statt "hier klicken" (z. B. "Werkzeuge ansehen").
- Saubere, eindeutige URLs/Pfade pro Werkzeug (`/kiko/`, künftig `/vokabeltrainer/` etc.), Eintrag in `sitemap.xml` nicht vergessen.
- Vorhanden auf der Startseite, bei jedem neuen Werkzeug mit übernehmen bzw. pro Seite anpassen:
  - `robots.txt` im Root, erlaubt gängige KI-Crawler (GPTBot, ClaudeBot, Google-Extended, CCBot) explizit.
  - Strukturierte Daten (JSON-LD, `schema.org/WebSite`) auf der Startseite — für ein eigenständiges Werkzeug mit eigener Seite passendes Schema ergänzen (z. B. `SoftwareApplication` oder `LearningResource`).
  - Open-Graph- und Twitter-Meta-Tags, `<link rel="canonical">` — pro Seite mit eigenem Titel/Text, nicht kopiert.

### 5. Marke & Design-Konsistenz

- Farbpalette lebt als CSS-Variablen im `:root`-Block von `index.html` — neue Farben dort als Variable anlegen, keine neuen Hex-Werte verstreut im Markup einführen.
- Aktuelle Palette: `--night` (dunkles Navy, Hero/Footer), `--paper`/`--sand`/`--sand-deep` (warme, helle Dünen-Töne), `--brand-teal` (gedeckter Salbei-/Pinienton `#5C8B7A`, Akzent- und CTA-Farbe).
- Kein Orange/Warnfarben-Ton als Akzentfarbe — bewusste Entscheidung, siehe Verlauf dieses Projekts.
- Akzentfarbe soll aus der Natur-/Dünen-/Berg-Bildsprache der Seite kommen: gedeckt statt grell, darf aber im Kontrast zum dunklen Hero und zu den warmen Sandtönen auffallen.
- Neue Kacheln/Icons im gleichen Illustrationsstil (flache Formen, abgerundete Ecken, gleiche Farbvariablen) halten.

### 6. Datenschutz & Sicherheit pro Werkzeug

Vorbild ist Kiko: alles bleibt im Browser-Tab, nichts wird verschickt oder dauerhaft gespeichert. Bei jedem Werkzeug konkret prüfen:

- **Kein Cookie-Einsatz** (weder eigene noch von Drittanbietern).
- **Keine Netzwerk-Requests, die Nutzereingaben verschicken** — kein `fetch`/`XHR`/Formular-Submit an einen Server, weder eigenen noch fremden. Rein clientseitiges JavaScript. Ausnahme: normale Links, die bewusst eine andere Seite öffnen (z. B. zu claude.ai) — das ist Navigation, kein Datenversand.
- **Zustand nur lokal im Browser halten**, bewusst über `sessionStorage` (verschwindet beim Schließen des Tabs — Standardfall, siehe Kikos Fußzeilentext "Deine Eingaben bleiben in diesem Browser-Tab und verschwinden beim Schließen") oder `localStorage` (bleibt bestehen, nur wenn das Werkzeug das wirklich braucht, z. B. Vokabel-Fortschritt über mehrere Besuche). Die Wahl den Nutzenden in einem kurzen Satz in der Fußzeile erklären.
- **Keine personenbezogenen Daten verlangen**, die für die Funktion nicht zwingend nötig sind (keine echten Namen, Adressen, Fotos, Kontodaten abfragen). Wo Freitextfelder das ermöglichen könnten (z. B. bei Zielgruppen Kinder/Jugendliche/Schule), einen Hinweis wie Kikos "Keine echten Daten eingeben" einbauen.
- **Kein Zwischenspeichern über die Sitzung hinaus** ohne Hinweis: kein automatisches Hochladen, kein "in der Cloud sichern"-Feature ohne ausdrückliche, sichtbare Aktion der Nutzenden.
- Deckt sich mit Abschnitt 3 (Performance): keine Analytics-, Tracking- oder Fingerprinting-Skripte, keine externen Fonts/Bilder/CDNs — die verschicken sonst unbemerkt Daten (IP-Adresse, User-Agent) an Dritte.
- Bei `target="_blank"`-Links immer `rel="noopener"` setzen (verhindert, dass die geöffnete Seite Zugriff auf das öffnende Tab bekommt).

## Neues Werkzeug prüfen, bevor es ins Repo bzw. auf die Startseite kommt

Wird ein neues Werkzeug (eigener Unterordner mit eigener `index.html`) fertig und soll als Kachel auf die Startseite oder allgemein ins Repo, dann vorher explizit prüfen lassen — z. B. mit "Prüf `<ordner>/` gegen CLAUDE.md, bevor ich es aufnehme". Der Ablauf dabei:

1. **Kachel-Konvention einhalten** — Aufbau, Zustände (fertig/Platzhalter) und Pflichtangaben wie in [README.md](README.md#kachel-vorlage) beschrieben.
2. **Abschnitte 1–4 dieser Datei komplett gegen die neue Seite prüfen**, nicht nur gegen die Startseite: Kontrast der dort verwendeten Farben, Landmark-/Überschriftenstruktur, Tastaturbedienbarkeit inkl. Fokus-Reihenfolge, eigene Meta-Angaben (Title, Description, canonical, OG-Tags).
3. **Abschnitt 6 (Datenschutz & Sicherheit) explizit durchgehen**: keine Cookies, keine Netzwerk-Requests mit Nutzereingaben, Zustand nur in session-/localStorage, keine unnötigen personenbezogenen Daten, keine Tracking-/Analytics-Skripte.
4. **Selbstständigkeit prüfen**: keine externen Ressourcen (Schriften, Skripte, Bilder, Tracking, CDNs), keine Zugangsschlüssel im Code, kein Server-Teil — sonst gehört es in ein eigenes Projekt und die Kachel verlinkt nur darauf.
5. **Interaktive Widgets** (eigene Buttons/Auswahl-Komponenten mit ARIA-Rollen wie `radiogroup`, `tablist` o. Ä.) auf das jeweils passende Tastaturmuster nach WAI-ARIA APG prüfen, nicht nur auf Klickbarkeit — Beispiel: die Wer-bist-du-Auswahl in Kiko nutzt `role="radiogroup"`/`role="radio"` und braucht deshalb Pfeiltasten-Navigation mit Roving-Tabindex, nicht nur Tab+Enter.
6. **Ergebnis kurz zusammenfassen**: was wurde geprüft, was behoben, was bewusst offen gelassen (mit Begründung) — bevor die Kachel auf der Startseite verlinkt wird.

## Projektregeln (siehe auch README.md)

- Keine Anmeldung, keine Cookies, keine Zugangsschlüssel im Code.
- Keine Inhalte von fremden Servern laden.
- Werkzeuge mit Server-Teil oder Geheimnissen gehören in ein eigenes Projekt, nicht hierher.
