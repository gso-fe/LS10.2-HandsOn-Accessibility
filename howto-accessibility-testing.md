# How-To: Accessibility-Testing mit den Chrome-DevTools

Diese Anleitung zeigt, wie du die drei Demo-Seiten zum BFSG mit den Chrome-DevTools untersuchst und vergleichst.

| Datei | Struktur/ARIA | Farbkontrast | Lernziel |
| --- | --- | --- | --- |
| `bfsg-schlecht.html` | fehlerhaft | offensichtlich schlecht | Typische Fehler im Accessibility-Tree erkennen |
| `bfsg-kontrast-subtil.html` | korrekt | subtil schlecht | Kontrastfehler finden, die man nicht sofort sieht |
| `bfsg-gut.html` | korrekt | gut | Referenz: so sieht es richtig aus |

---

## 0. Vorbereitung

1. Alle drei Dateien in denselben Ordner legen und in VS Code mit **Live Server** öffnen („Go Live“).
2. Seite in **Chrome** öffnen, DevTools mit `F12` bzw. `Strg+Umschalt+I` starten (Mac: `Cmd+Option+I`).
3. Tipp: Zwei Chrome-Fenster nebeneinander öffnen, um zwei Varianten direkt zu vergleichen.

---

## 1. Accessibility-Tree untersuchen

Der Accessibility-Tree zeigt, was Screenreader von der Seite „sehen“: **Rolle, Name, Zustand**.

### So öffnest du ihn

- **Einzelnes Element:** Elements-Panel → Element anklicken → rechts Tab **„Accessibility“**
  (ggf. über `»` einblenden). Dort stehen *Computed Properties* wie `Name`, `Role`, `expanded`, `invalid`.
- **Ganze Seite:** Im Elements-Panel oben auf das Symbol **„Switch to Accessibility Tree view“** klicken.
  Falls nicht sichtbar: Accessibility-Tab → Häkchen bei **„Enable full-page accessibility tree“** → DevTools neu laden.

### Was du vergleichen solltest

| Prüfpunkt | `bfsg-schlecht.html` | `bfsg-gut.html` / `bfsg-kontrast-subtil.html` |
| --- | --- | --- |
| Seitensprache | kein `lang` | `lang="de"` |
| Landmarks | keine, nur `generic` | `banner`, `navigation`, `main`, `contentinfo` |
| Überschriften | `<div>` → Rolle `generic` | `heading` mit `level 1/2/3` |
| Navigation | `<span>` → kein Link, nicht per Tab erreichbar | `link` mit sprechendem Namen |
| Infografik | `img` ohne Name | `image` mit Name aus `alt` |
| Deko-Trenner | Name „trenner_final_v2.svg“ | ignoriert (`alt=""`) |
| Tabelle | nur `cell` | `columnheader` / `rowheader`, Caption als Name |
| FAQ | `generic`, kein Zustand | `button` mit `expanded: false` → `true` |
| E-Mail-Feld | Name nur aus `placeholder` | `textbox` „E-Mail-Adresse“, `required`, Beschreibung |
| Fehlerzustand | nur rote Umrandung | `invalid: true` + Fehlertext als Beschreibung |
| Erfolgsmeldung | einfacher Text | `status` (Live-Region) |

### Zustände live beobachten

1. Im Accessibility-Tree den **FAQ-Button** markieren.
2. Auf der Seite den Button anklicken → im Accessibility-Tab ändert sich `expanded` von `false` zu `true`.
3. Formular leer absenden → beim Eingabefeld erscheint `invalid: true`, die Beschreibung enthält den Fehlertext.
4. In `bfsg-schlecht.html` passiert dasselbe nur optisch – im Tree ändert sich nichts.

### Tastatur-Test (ergänzend)

- Mit `Tab` durch die Seite gehen.
- **Schlecht:** Fokus unsichtbar, Navigation nicht erreichbar, FAQ und „Absenden“ nicht bedienbar.
- **Gut:** „Zum Inhalt springen“ erscheint zuerst, Fokusrahmen überall sichtbar.
- **Subtil:** Fokusrahmen vorhanden, aber kaum zu erkennen (Kontrastproblem, siehe unten).

---

## 2. Farbkontrast messen

### Anforderungen (WCAG 2.x, Stufe AA)

| Element | Mindestkontrast |
| --- | --- |
| Normaler Text | 4,5 : 1 |
| Großer Text (ab 24 px oder ab ca. 18,7 px fett) | 3 : 1 |
| UI-Komponenten, Feldränder, Fokusrahmen, Icons | 3 : 1 |

### Methode A: Element-Picker (schnell)

1. `Strg+Umschalt+C` (Mac: `Cmd+Shift+C`) drücken.
2. Mit der Maus über Texte fahren.
3. Im Tooltip unter **Accessibility** steht **Contrast** mit Wert und ✓ oder ⚠.

### Methode B: Farbwähler im Styles-Panel (genau)

1. Element markieren → im **Styles**-Panel auf das Farbquadrat neben `color` klicken.
2. Der Farbwähler zeigt das **Kontrastverhältnis** und Linien für AA/AAA.
3. Ein Klick auf das Symbol neben dem Wert schlägt eine passende Farbe vor.

### Methode C: CSS-Übersicht (ganze Seite)

1. DevTools-Menü `⋮` → **More tools** → **CSS Overview** → **Capture overview**.
2. Bereich **Colors** → **Contrast issues** listet alle betroffenen Textstellen.
3. Ein Klick auf einen Eintrag zeigt die zugehörigen Elemente.

### Was du in `bfsg-kontrast-subtil.html` finden solltest

| Element | Kontrast | Ergebnis |
| --- | --- | --- |
| Fließtext | 3,55 : 1 | ⚠ durchgefallen |
| Links | 3,61 : 1 | ⚠ durchgefallen |
| Weiß auf Teal (Kopfzeile) | 3,74 : 1 | ⚠ durchgefallen |
| Weiß auf Orange (Button) | 3,56 : 1 | ⚠ durchgefallen |
| H1/H2 in Teal | 3,32 : 1 | ✓ bestanden (großer Text) |
| FAQ-Button in Teal | 3,23 : 1 | ⚠ durchgefallen (normaler Text) |
| Fußzeile (dunkel) | 4,01 : 1 | ⚠ knapp durchgefallen |

**Aha-Effekt:** Überschriften und FAQ-Button haben fast dieselbe Farbe – aber nur die Überschriften bestehen, weil sie groß sind.

### Nicht-Text-Kontraste manuell prüfen

Automatische Tools prüfen meist nur Text. Feldrahmen und Fokusrahmen musst du selbst messen:

1. Eingabefeld markieren → im Styles-Panel `border-color` anklicken → mit der **Pipette** den Hintergrund aufnehmen
   bzw. die Farben notieren und vergleichen. Ergebnis in der subtilen Variante: **1,49 : 1** (nötig: 3 : 1).
2. Fokusrahmen sichtbar machen: Element markieren → Styles-Panel → **`:hov`** → **`:focus-visible`** aktivieren.
   Ergebnis: **1,31 : 1** – der Rahmen ist praktisch unsichtbar.

---

## 3. Lighthouse-Audit

1. DevTools → Tab **Lighthouse**.
2. Nur Kategorie **Accessibility** auswählen, Gerät *Desktop* oder *Mobile*.
3. **Analyze page load** starten.

Erwartung:

- `bfsg-schlecht.html`: niedriger Score, viele Fehler (Namen, Sprache, Kontrast, Zoom gesperrt …)
- `bfsg-kontrast-subtil.html`: hoher Score, aber **Kontrastfehler** als Hauptproblem
- `bfsg-gut.html`: Score nahe 100

Wichtig: Ein hoher Score heißt **nicht**, dass die Seite barrierefrei ist. Lighthouse findet nur einen Teil der Probleme
(z. B. weder Fokusrahmen- noch Feldrand-Kontraste).

---

## 4. Sehschwächen simulieren

1. DevTools-Menü `⋮` → **More tools** → **Rendering**.
2. Unter **Emulate vision deficiencies** z. B. wählen:
   - **Blurred vision** – verschwommenes Sehen
   - **Reduced contrast** – Kontrastschwäche
   - **Protanopia / Deuteranopia** – Rot-Grün-Schwäche
   - **Achromatopsia** – keine Farbwahrnehmung
3. Besonders aufschlussreich bei `bfsg-kontrast-subtil.html`: Mit *Reduced contrast* wird der Fließtext schwer lesbar.
4. Mit *Deuteranopia* in `bfsg-schlecht.html`: Die rote Fehlerumrandung ist kaum noch erkennbar –
   deshalb dürfen Fehler nie **nur** über Farbe vermittelt werden.

---

## 5. Kurz-Checkliste

- [ ] Accessibility-Tree: Rollen, Namen und Zustände korrekt?
- [ ] Landmarks und Überschriftenhierarchie vorhanden?
- [ ] Zustandswechsel (`expanded`, `invalid`) im Tree sichtbar?
- [ ] Alle Texte ≥ 4,5 : 1 (große Texte ≥ 3 : 1)?
- [ ] Feldränder, Icons und Fokusrahmen ≥ 3 : 1?
- [ ] Mit Tab vollständig bedienbar, Fokus sichtbar?
- [ ] Lighthouse ohne Fehler – **und** trotzdem manuell geprüft?
- [ ] Zum Abschluss mit einem echten Screenreader (NVDA, VoiceOver) getestet?
