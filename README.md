# ganzeinfach.geprog.com

Kleine Werkzeuge und Anleitungen von GEPROG. Direkt im Browser, ohne Anmeldung, ohne Tracking.

Die Seite besteht nur aus statischen Dateien. Es gibt keinen Server-Code, keine Datenbank und keine externen Dienste.

## Aufbau

```
index.html        Startseite mit den Kacheln
kiko/index.html   Kiko, die geführte Anleitung für Einsteiger
assets/           Logo (logo-white.svg) und gemeinsame Dateien
<werkzeug>/       Ein weiterer Unterordner pro Werkzeug, jeweils mit eigener index.html
```

## Lokal ansehen

`index.html` im Browser öffnen.

## Ein neues Werkzeug hinzufügen

1. Ordner im Root anlegen, zum Beispiel `vokabeltrainer/`, mit einer eigenen, in sich geschlossenen `index.html` darin (eigenes `<style>`, bei Bedarf eigenes `<script>`, keine externen Ressourcen).
2. Auf der Startseite eine Kachel in `index.html` in der `<ul class="tiles">` ergänzen — Vorlage siehe unten.
3. Links zu Impressum und Datenschutz in die Fußzeile des Werkzeugs setzen und einen Link zurück zur Startseite.
4. Die URL in `sitemap.xml` ergänzen.

### Kachel-Vorlage

Es gibt genau zwei Zustände. Fertiges, klickbares Werkzeug:

```html
<li>
  <a class="tile" href="ORDNERNAME/">
    <div class="art" aria-hidden="true">
      <!-- eigenes Inline-SVG-Icon, kein Rasterbild; gleicher Stil: flache Formen,
           abgerundete Ecken, Farben aus den CSS-Variablen in :root -->
    </div>
    <div class="tile-body">
      <h3>Name des Werkzeugs</h3>
      <p>Ein Satz in einfachen Worten: was es tut und für wen.</p>
      <ul class="tags"><li>Schlagwort</li><li>Schlagwort</li></ul>
      <span class="open">Öffnen</span>
    </div>
  </a>
</li>
```

Noch nicht fertiges Werkzeug (Platzhalter):

```html
<li>
  <div class="tile soon" role="group" aria-label="Name des Werkzeugs, in Arbeit">
    <div class="art" aria-hidden="true"><!-- Icon, darf reduziert/unfertig wirken --></div>
    <div class="tile-body">
      <h3>Name des Werkzeugs</h3>
      <p>Ein Satz, was es können wird.</p>
      <ul class="tags"><li>Schlagwort</li></ul>
      <span class="badge">Bald da</span>
    </div>
  </div>
</li>
```

Dabei gilt:

- Icon immer als Inline-SVG (kein `<img>`), damit es scharf bleibt und keinen zusätzlichen Request braucht.
- 1–3 kurze Tags, keine ganzen Sätze.
- Der fertige Zustand endet immer mit `<span class="open">Öffnen</span>`, der Platzhalter-Zustand immer mit `<span class="badge">Bald da</span>` — der Unterschied zwischen beiden Zuständen darf nie nur über Farbe transportiert werden (Text/Badge macht es zusätzlich klar), siehe [CLAUDE.md](CLAUDE.md).
- Platzhalter-Kacheln sind ein `<div>` mit `role="group"` und `aria-label`, keine `<a>` — sie sind nicht klickbar.

Regeln, damit die Seite ihr Versprechen hält:

- Keine Anmeldung, keine Cookies, keine Zugangsschlüssel im Code.
- Keine Inhalte von fremden Servern laden (keine externen Schriften, Skripte, Bilder oder Statistik-Dienste). Alles liegt im Repo.
- Werkzeuge mit Server-Teil oder geheimen Schlüsseln gehören nicht hierher, sondern in ein eigenes Projekt. Die Kachel kann dann darauf verlinken.
- Jedes Werkzeug erfüllt für sich die Prüfpunkte aus [CLAUDE.md](CLAUDE.md) (Kontrast, Barrierefreiheit, Meta-Angaben).

## Veröffentlichen

Übernimmt der Techniker (Server, DNS, HTTPS) — nicht Teil dieses Repos.
