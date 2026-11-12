# LS10.2 – Hands-On Accessibility

Praktischer Einstieg in das Thema **Web-Accessibility**: drei fast identische HTML-Demo-Seiten rund um
das Barrierefreiheitsstärkungsgesetz (BFSG), an denen sich typische Barrieren und ihre Behebung direkt
im Code, im Accessibility-Tree, mit Tastatur und mit Screenreader nachvollziehen lassen.

> **Begriffe:** „Barrierefreiheit" und „Accessibility" bezeichnen dasselbe — Barrierefreiheit ist der
> deutsche, rechtliche Begriff (BITV, BFSG), Accessibility der international übliche, technische Begriff
> (WCAG, ARIA, Browser-DevTools). Beide Begriffe sollten Sie sicher und synonym verwenden können.

## Einstieg

1. **Einordnen:** `howto_barrierefreiheit.md` aus dem Repository **LS10.2-Guided-Fallstudie** —
   Abgrenzung zu Usability/UX, Rechtslage in Deutschland (BITV 2.0, BFSG) und die vier WCAG-Prinzipien
   (POUR). Liegt hier nicht noch einmal vor, da es aus diesem Repository schon bekannt ist.
2. **Vergleichen:** die drei Demo-Seiten (`bfsg-schlecht.html`, `bfsg-kontrast-subtil.html`,
   `bfsg-gut.html`) im Browser öffnen und gegenüberstellen.
3. **Testen:** [`howto-accessibility-testing.md`](howto-accessibility-testing.md) — Accessibility-Tree,
   Farbkontrast, Lighthouse-Audit und Sehschwächen-Simulation mit den Chrome-DevTools.
4. **Bedienen:** [`howto-navigate.md`](howto-navigate.md) — dieselben Seiten per Tastatur und mit
   Screenreader (NVDA, VoiceOver/Rotor) durchgehen.
5. **Nachschlagen:** [`glossar-accessibility.md`](glossar-accessibility.md) — jeder verwendete Begriff und
   jedes ARIA-/HTML-Attribut, jeweils mit Fundstelle in den drei Demo-Seiten.

## Die drei Demo-Seiten

| Datei | Struktur/ARIA | Farbkontrast | Lernziel |
|---|---|---|---|
| `bfsg-schlecht.html` | fehlerhaft | offensichtlich schlecht | typische Fehler im Accessibility-Tree erkennen |
| `bfsg-kontrast-subtil.html` | korrekt | subtil schlecht | Kontrastfehler finden, die man nicht sofort sieht |
| `bfsg-gut.html` | korrekt | gut | Referenz: so sieht es richtig aus |

Alle drei zeigen denselben Inhalt (Infografik, FAQ-Akkordeon, Checkliste-Formular) — nur Struktur, ARIA
und Kontrast unterscheiden sich.

## Quick Start

Die Seiten sind reines HTML/CSS/JS ohne Build-Schritt. Am einfachsten:

1. Ordner in VS Code öffnen und mit der **Live Server**-Erweiterung starten („Go Live").
2. Alternativ die `.html`-Dateien direkt per Doppelklick im Browser öffnen.
3. Chrome-DevTools (`F12`) für Accessibility-Tree, Kontrastmessung und Lighthouse nutzen (siehe
   `howto-accessibility-testing.md`).

## Dateien im Überblick

| Datei | Inhalt |
|---|---|
| `howto-accessibility-testing.md` | Prüfung der Demo-Seiten mit den Chrome-DevTools |
| `howto-navigate.md` | Bedienung der Demo-Seiten per Tastatur und Screenreader |
| `glossar-accessibility.md` | Begriffe, ARIA- und HTML-Attribute mit Fundstellen in den Demos |
| `bfsg-schlecht.html` / `bfsg-kontrast-subtil.html` / `bfsg-gut.html` | die drei Demo-Seiten |

