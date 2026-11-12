# How-To: Navigation mit Tastatur und Screenreader

Praktische Anleitung, wie Menschen ohne Maus Webseiten bedienen – und was dafür im Code stimmen muss. Ausprobieren lässt sich alles an den drei Demo-Seiten `bfsg-schlecht.html`, `bfsg-kontrast-subtil.html` und `bfsg-gut.html`. Begriffe erklärt `Glossar-Accessibility.md`.

**Inhalt**

1. [Warum das wichtig ist](#warum-das-wichtig-ist)
2. [Tastatur-Navigation (Tab)](#1-tastatur-navigation-tab)
3. [Der Fokusrahmen](#2-der-fokusrahmen)
4. [Screenreader-Navigation](#3-screenreader-navigation)
5. [Der Rotor (VoiceOver)](#4-der-rotor-voiceover)
6. [Tastatur vs. Screenreader](#5-tastatur-vs-screenreader)

---

## Warum das wichtig ist

Viele Menschen bedienen Websites ohne Maus: blinde Menschen mit Screenreader, Menschen mit motorischen Einschränkungen (Tastatur, Schalter, Sprachsteuerung) und Power-User. Alle Bedienelemente müssen deshalb **per Tastatur erreichbar und auslösbar** sein (WCAG 2.1.1). Screenreader setzen zusätzlich voraus, dass **Rolle, Name und Zustand** im Accessibility-Tree stimmen.

---

## 1) Tastatur-Navigation (Tab)

Die Tab-Taste springt nur zwischen **fokussierbaren** Elementen: Links, Buttons, Formularfeldern. Text, Überschriften und Bilder werden übersprungen.

| Taste                  | Wirkung                                                   |
| ---------------------- | --------------------------------------------------------- |
| `Tab` / `Umschalt+Tab` | Nächstes / voriges fokussierbares Element                 |
| `Enter`                | Link folgen, Button auslösen, Formular absenden           |
| `Leertaste`            | Button auslösen, Checkbox umschalten, Seite scrollen      |
| `Pfeiltasten`          | Innerhalb von Widgets: Optionsfelder, Auswahllisten, Tabs |
| `Esc`                  | Dialog oder Menü schließen                                |

**Worauf es ankommt:**

- **Erreichbar:** Echte `<a href>` und `<button>` statt `<div onclick>`/`<span onclick>`.
- **Sichtbar:** Der Fokusrahmen zeigt, wo man gerade ist (siehe Abschnitt 2).
- **Logische Reihenfolge:** Tab folgt der Reihenfolge im HTML. Keine positiven `tabindex`-Werte.
- **Abkürzung:** Ein Skiplink erspart das Durchtabben der Navigation auf jeder Seite.

**In den Demos:** In _schlecht_ erreicht `Tab` nur zwei Elemente: den Link „hier“ und das E-Mail-Feld. Navigation, FAQ und „Absenden“ sind unerreichbar, und den Fokus sieht man nicht. In _gut_ erscheint zuerst der Skiplink, danach folgen Navigation, Links, FAQ-Buttons, Feld und Button mit sichtbarem Rahmen. In _subtil_ ist der Rahmen vorhanden, aber kaum zu erkennen.

---

## 2) Der Fokusrahmen

Der Fokusrahmen (Focus Indicator, Focus Ring) ist für Tastaturnutzer das, was der Mauszeiger für Mausnutzer ist: Er zeigt, **wo man gerade ist** und was `Enter` auslösen würde. Ohne ihn ist Tastaturbedienung Blindflug.

### Anforderungen (WCAG)

| Kriterium                     | Stufe          | Anforderung                                                                                                       |
| ----------------------------- | -------------- | ----------------------------------------------------------------------------------------------------------------- |
| 2.4.7 Fokus sichtbar          | AA             | Es muss **einen** sichtbaren Fokus geben.                                                                         |
| 1.4.11 Nicht-Text-Kontrast    | AA             | Fokusrahmen mindestens **3 : 1** gegenüber dem Hintergrund.                                                       |
| 2.4.11 Fokus nicht verdeckt   | AA (WCAG 2.2)  | Fokussiertes Element darf nicht vollständig von Sticky-Header, Cookie-Banner o. Ä. verdeckt sein.                 |
| 2.4.13 Fokus-Erscheinungsbild | AAA (WCAG 2.2) | Rahmen mindestens 2 CSS-Pixel dick um das ganze Element, 3 : 1 Kontrast zwischen fokussiert und nicht fokussiert. |

### Die CSS-Selektoren

| Selektor         | Greift wann                                                                                           | Einsatz                                                 |
| ---------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| `:focus`         | Bei **jedem** Fokus, auch nach Mausklick                                                              | Selten nötig; nicht zum Entfernen des Rahmens verwenden |
| `:focus-visible` | Wenn der Browser einen sichtbaren Fokus für sinnvoll hält – v. a. bei Tastatur, bei Textfeldern immer | **Standard** für eigene Fokusrahmen                     |
| `:focus-within`  | Wenn das Element **oder ein Kind** den Fokus hat                                                      | Ganze Karte oder Formulargruppe hervorheben             |

### Umsetzung – empfohlenes Muster

So ist es in `bfsg-gut.html` umgesetzt:

```css
:root {
  --fokus: #1d4ed8; /* Kontrast auf Weiß deutlich über 3 : 1 */
}

:focus-visible {
  outline: 3px solid var(--fokus);
  outline-offset: 2px;
}
```

**Warum `outline`?**

- Verändert das **Layout nicht** (anders als `border`) – nichts springt beim Fokussieren.
- Folgt in modernen Browsern dem `border-radius`.
- Bleibt im **Windows-Kontrastmodus** (`forced-colors`) sichtbar. `box-shadow` wird dort entfernt!

`outline-offset` setzt den Rahmen etwas vom Element ab – so ist er auch auf gleichfarbigen Buttons sichtbar.

### Typische Fehler

| Fehler                                      | Problem                                           | Demo                          |
| ------------------------------------------- | ------------------------------------------------- | ----------------------------- |
| `*:focus { outline: none; }`                | Fokus komplett unsichtbar                         | ✗ `bfsg-schlecht.html`        |
| Blasser Rahmen, z. B. Hellgrün auf Creme    | Formal vorhanden, praktisch unsichtbar (1,31 : 1) | K `bfsg-kontrast-subtil.html` |
| Fokus nur über Farbänderung des Textes      | Kaum wahrnehmbar, oft unter 3 : 1                 | –                             |
| `border` statt `outline`                    | Layout springt beim Fokussieren                   | –                             |
| Sticky-Header verdeckt fokussiertes Element | Verstoß gegen WCAG 2.4.11                         | –                             |

### Fokusrahmen prüfen

1. Mit `Tab` durch die Seite gehen – ist der Rahmen **überall** gut zu sehen?
2. In den Chrome-DevTools: Element markieren → Styles → `:hov` → **`:focus-visible`** aktivieren. Der Rahmen bleibt stehen und lässt sich untersuchen.
3. Rahmenfarbe gegen den Hintergrund messen (Farbwähler im Styles-Panel): mindestens **3 : 1**.
4. Optional: Windows-Kontrastmodus einschalten (`Alt+Umschalt+Druck` bzw. Einstellungen → Barrierefreiheit → Kontrastdesigns) und erneut tabben.

---

## 3) Screenreader-Navigation

Screenreader lesen nicht einfach von oben nach unten. Nutzer **springen gezielt** – ähnlich wie Sehende eine Seite überfliegen. Das funktioniert nur mit semantischem HTML.

| Strategie                 | NVDA (Lesemodus)            | VoiceOver iOS (Rotor)          | Voraussetzung im Code                     |
| ------------------------- | --------------------------- | ------------------------------ | ----------------------------------------- |
| Überschriften überfliegen | `H`, `1`–`6`                | Rotor „Überschriften“, wischen | `<h1>`–`<h6>`                             |
| Zu Bereichen springen     | `D`                         | Rotor „Orientierungspunkte“    | `<header>`, `<nav>`, `<main>`, `<footer>` |
| Alle Links auflisten      | `NVDA+F7`                   | Rotor „Links“                  | `<a href>` mit verständlichem Text        |
| Formularfelder anspringen | `F`, `E`                    | Rotor „Formularsteuerelemente“ | `<input>` mit `<label>`                   |
| Buttons anspringen        | `B`                         | Rotor „Tasten“                 | `<button>`                                |
| Tabellen lesen            | `T`, dann `Strg+Alt+Pfeile` | Rotor „Tabellen“               | `<th scope>`, `<caption>`                 |
| Element für Element       | `Pfeil runter`              | nach rechts wischen            | sinnvolle Reihenfolge                     |

**Was der Screenreader ansagt:** Name + Rolle + Zustand, z. B. _„Gilt das BFSG auch für B2B-Angebote?, Schalter, reduziert“_. Nach dem Auslösen: _„erweitert“_. Statusmeldungen in einer Live-Region werden angesagt, ohne dass der Fokus wandert.

**In den Demos:** In _schlecht_ findet NVDA mit `H` keine einzige Überschrift, mit `D` keinen Bereich, die Elementliste (`NVDA+F7`) enthält nur „hier“ als Link. Das FAQ wird als bloßer Text vorgelesen, ohne Zustand. In _gut_ und _subtil_ funktionieren alle Sprungtasten, das FAQ meldet „reduziert/erweitert“, Fehler und Erfolg beim Formular werden angesagt.

---

## 4) Der Rotor (VoiceOver)

Der **Rotor** ist die zentrale Navigationshilfe von VoiceOver auf iPhone, iPad und Mac. Man wählt damit zuerst, **wonach** man springen möchte (Überschriften, Links, Formularfelder …), und springt dann von Treffer zu Treffer. Er erfüllt dieselbe Aufgabe wie die Buchstaben-Sprungtasten (`H`, `K`, `F` …) und die Elementliste von NVDA.

### iPhone / iPad

1. VoiceOver einschalten (am besten per Kurzbefehl: Dreifachklick auf die Seitentaste).
2. **Rotor drehen:** Zwei Finger auf den Bildschirm legen und drehen, als würde man einen Drehknopf bewegen. VoiceOver sagt die Kategorie an: „Überschriften“, „Links“, „Orientierungspunkte“ …
3. **Springen:** Mit einem Finger **nach unten wischen** = nächster Treffer dieser Kategorie, **nach oben wischen** = voriger.
4. **Auslösen:** Doppeltippen.

Welche Kategorien im Rotor erscheinen, legt man fest unter **Einstellungen → Bedienungshilfen → VoiceOver → Rotor**. Für Webtests sinnvoll: Überschriften, Links, Orientierungspunkte, Formularsteuerelemente, Tabellen.

### Mac

| Aktion             | Tasten                                            |
| ------------------ | ------------------------------------------------- |
| VoiceOver an/aus   | `Cmd+F5`                                          |
| Rotor öffnen       | `VO+U` (`VO` = `Ctrl+Option`)                     |
| Kategorie wechseln | `Pfeil links/rechts`                              |
| Eintrag wählen     | `Pfeil hoch/runter` oder Anfangsbuchstaben tippen |
| Hinspringen        | `Enter`                                           |
| Rotor schließen    | `Esc`                                             |

Auf dem Mac zeigt der Rotor alle Treffer einer Kategorie **als Liste** – praktisch zum Testen: Man sieht sofort alle Überschriften oder alle Linktexte einer Seite auf einen Blick.

### Was der Rotor über eine Seite verrät

Der Rotor ist ein schneller Struktur-Test: Er zeigt nur, was im Code **semantisch** ausgezeichnet ist.

| Rotor-Kategorie        | `bfsg-schlecht.html`                                          | `bfsg-gut.html`                                                                    |
| ---------------------- | ------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| Überschriften          | leer                                                          | H1 „Das Barrierefreiheitsstärkungsgesetz (BFSG)“, mehrere H2, zwei H3              |
| Orientierungspunkte    | leer                                                          | Banner, Navigation „Hauptnavigation“, Hauptbereich, Formular, Inhaltsinformationen |
| Links                  | „hier“                                                        | „Zum Inhalt springen“, „Wer ist betroffen?“, „FAQ der Bundesfachstelle …“ …        |
| Formularsteuerelemente | Textfeld „E-Mail“ (Name nur aus `placeholder`), kein Schalter | „E-Mail-Adresse, erforderlich“, Schalter                                           |

Ist eine Rotor-Kategorie leer, obwohl die Seite sichtbar Überschriften oder Bereiche hat, fehlt die Semantik im Code.

---

## 5) Tastatur vs. Screenreader

|                   | Tastatur (ohne Screenreader)                          | Screenreader                                                     |
| ----------------- | ----------------------------------------------------- | ---------------------------------------------------------------- |
| Sieht die Seite   | ja                                                    | nein (oder eingeschränkt)                                        |
| Navigiert über    | `Tab` (nur Bedienelemente)                            | Sprungtasten/Rotor (alle Inhalte)                                |
| Braucht vor allem | Fokussierbarkeit, sichtbaren Fokusrahmen, Reihenfolge | Semantik: Rolle, Name, Zustand, Struktur                         |
| Typischer Fehler  | `outline: none`, `<div onclick>`                      | `<div>` statt Überschrift, fehlende Labels, kein `aria-expanded` |

Faustregel: **Erst mit `Tab` testen, dann mit Screenreader.** Was per Tastatur nicht geht, geht auch mit Screenreader nicht.
