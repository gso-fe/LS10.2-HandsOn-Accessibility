# Glossar v2: Accessibility in den Demo-Seiten

Kurzfassung: nur Begriffe, die in `bfsg-schlecht.html`, `bfsg-kontrast-subtil.html` und `bfsg-gut.html` wirklich vorkommen, plus fünf wichtige Ergänzungen.

**So liest du die Tabellen:** Die beiden rechten Spalten zeigen, was du in den Demo-Seiten findest.

- **Schlecht** = `bfsg-schlecht.html`
- **Gut / Subtil** = `bfsg-gut.html` und `bfsg-kontrast-subtil.html`. Beide haben dieselbe, korrekte Struktur; *Subtil* unterscheidet sich nur bei den Farben (siehe Abschnitt Kontrast).
- „–“ = kommt in dieser Variante nicht vor

---

## Grundbegriffe

| Begriff | Bedeutung | Schlecht | Gut / Subtil |
| --- | --- | --- | --- |
| **Accessibility-Tree** | Was Screenreader von der Seite erhalten: jedes Element mit **Rolle**, **Name** und **Zustand**. In Chrome: DevTools → Elements → Tab „Accessibility“. | fast nur `generic` | vollständig |
| **Rolle** | Was ein Element ist (`button`, `link`, `heading` …). Kommt automatisch aus dem passenden HTML-Element. | fehlt meist | korrekt |
| **`generic`** | Rolle von `<div>` und `<span>`: keine Bedeutung. Häufiges Zeichen für fehlende Semantik. | überall | nur für Layout-Container |
| **Accessible Name** | Was der Screenreader als Namen vorliest. Quelle (nach Priorität): `aria-labelledby` → `aria-label` → `<label>`/`alt`/`<caption>` → Textinhalt → `placeholder`. | fehlt oder nur aus `placeholder` | überall sinnvoll |
| **Landmarks** | Seitenregionen zum direkten Anspringen: `<header>` = banner, `<nav>` = navigation, `<main>` = main, `<footer>` = contentinfo. | keine | alle vier + Formular |
| **Überschriften `<h1>`–`<h3>`** | Rolle `heading` mit Ebene. Screenreader-Nutzer springen damit durch die Seite. | nur `<div class="h1">` | H1, H2, H3 |

## ARIA-Attribute

| Attribut | Bedeutung | Schlecht | Gut / Subtil |
| --- | --- | --- | --- |
| `aria-label` | Name direkt als Text, z. B. `<nav aria-label="Hauptnavigation">`. | – | Navigation |
| `aria-labelledby` | Name aus einem anderen Element **per ID** (siehe Hinweis unten). | – | Formular |
| `aria-describedby` | Verknüpft Hinweis- und Fehlertext **per ID**; wird nach dem Namen vorgelesen. | – | E-Mail-Feld |
| `aria-expanded` | Zustand „auf-/zugeklappt“ am **Auslöser**. JavaScript muss ihn umschalten. | fehlt | FAQ-Buttons |
| `aria-controls` | Verweist **per ID** auf den gesteuerten Bereich. | – | FAQ-Buttons |
| `aria-invalid` | Markiert ein Feld als fehlerhaft („ungültige Eingabe“). | Fehler nur als rote Umrandung | E-Mail-Feld bei Fehler |
| `aria-hidden="true"` | Blendet Dekoratives für Screenreader aus. Nie auf fokussierbare Elemente. | – | Pfeil „▾“, Sternchen „*“ |
| `role="status"` | Live-Region: Änderungen werden **automatisch angesagt**, ohne Fokuswechsel. Muss beim Laden schon im DOM stehen. | Meldung wird nicht angesagt | Erfolgsmeldung |

> ### Hinweis: Was bedeutet „per ID“?
>
> Einige Attribute enthalten **keinen Text**, sondern den Wert eines `id`-Attributs. Der Browser sucht das Element mit dieser ID und übernimmt dessen Text.
>
> ```html
> <h2 id="checkliste-titel">Checkliste anfordern</h2>
> <form aria-labelledby="checkliste-titel"> … </form>
> ```
>
> Der Screenreader sagt: *„Checkliste anfordern, Formular“*. Der Text steht nur einmal im Code; ändert sich die Überschrift, ändert sich der Name mit.
>
> - **Mehrere IDs** mit Leerzeichen trennen; die Texte werden in dieser Reihenfolge verbunden: `aria-describedby="mail-hinweis mail-fehler"`.
> - **`aria-label` vs. `aria-labelledby`:** `aria-label` enthält den Text selbst, `aria-labelledby` verweist auf sichtbaren Text. Sichtbarer Text ist meist besser – Sehende und Screenreader-Nutzer bekommen denselben Namen.
> - **Arbeiten mit IDs:** `aria-labelledby`, `aria-describedby`, `aria-controls`, `aria-errormessage` und auch `<label for="…">`.
>
> **Typische Fehler** (der Browser meldet keinen davon!):
>
> - Tippfehler im Attribut: richtig ist `aria-labelledby` mit **zwei l**.
> - ID existiert nicht oder ist anders geschrieben (Groß-/Kleinschreibung zählt).
> - ID doppelt vergeben – IDs müssen auf der Seite eindeutig sein.
> - Raute davor: `aria-labelledby="#titel"` ist falsch, richtig ist `"titel"`.
>
> **Prüfen:** DevTools → Element markieren → Tab „Accessibility“ → *Name* zeigt, welcher Text ankommt und woher.

## HTML-Attribute und -Elemente

| Element / Attribut | Bedeutung | Schlecht | Gut / Subtil |
| --- | --- | --- | --- |
| `<html lang="de">` | Sprache der Seite → richtige Aussprache. | fehlt | vorhanden |
| `<title>` | Aussagekräftiger Seitentitel, wird zuerst angesagt. | nur „Seite“ | beschreibend |
| `user-scalable=no` | Im Viewport-Meta-Tag: **verhindert Zoomen** – ein Fehler. | gesetzt | nicht gesetzt |
| `alt="…"` | Textalternative für informative Bilder. | fehlt | Infografik |
| `alt=""` | Bild ist dekorativ und wird ignoriert. Nicht dasselbe wie fehlendes `alt`. | Dateiname als `alt` | Trennlinie |
| `<figure>` + `<figcaption>` | Bild mit verknüpfter Bildunterschrift. | – | Infografik |
| `<label for="…">` | Sichtbare, **per ID** verknüpfte Feldbeschriftung. | fehlt | E-Mail-Feld |
| `placeholder` | Beispieltext im Feld; **kein Ersatz für ein Label**. | statt Label | – |
| `required` | Pflichtfeld („erforderlich“). | – | E-Mail-Feld |
| `type="email"` + `autocomplete="email"` | Passende Tastatur auf Mobilgeräten, Browser schlägt Wert vor. | nur `type="text"` | vorhanden |
| `novalidate` | Browser-Validierung aus, damit eigene zugängliche Fehlermeldungen greifen. | – | Formular |
| `hidden` | Versteckt ein Element für alle, auch für Screenreader. | – (nur `display: none`) | FAQ-Antworten |
| `<caption>` | Tabellentitel, wird zum Namen der Tabelle. | fehlt | vorhanden |
| `<th scope="col/row">` | Spalten-/Zeilenköpfe; werden beim Navigieren mitgelesen. | nur `<td>` | vorhanden |
| `<button>` | Echter Schalter: fokussierbar, Enter und Leertaste funktionieren. | `<div onclick>` | FAQ, Absenden |
| `type="button"` | Button sendet kein Formular ab. | – | FAQ-Buttons |
| Linktext | Muss allein verständlich sein. | „hier“ | „FAQ der Bundesfachstelle …“ |

## Fokus und Tastatur

| Begriff | Bedeutung | Schlecht | Gut / Subtil |
| --- | --- | --- | --- |
| **Fokussierbar** | Per `Tab` erreichbar: Links mit `href`, Buttons, Formularfelder. `<div>`/`<span>` sind es nicht. | nur Link „hier“ + Feld | alle Bedienelemente |
| **Skiplink** | „Zum Inhalt springen“ – erscheint beim ersten `Tab` und überspringt Kopf und Navigation. | fehlt | vorhanden |
| `tabindex="-1"` | Element per Skript oder Skiplink fokussierbar, aber nicht per `Tab`. | – | `<main>` |
| `outline: none` | Entfernt den Fokusrahmen – ohne Ersatz ein schwerer Fehler. | gesetzt | – |
| `:focus-visible` | Fokusrahmen nur bei Tastaturbedienung. | – | Gut: deutlich · Subtil: zu blass |
| `element.focus()` | Fokus per JavaScript setzen, z. B. nach einem Fehler ins Feld. | – | nach Formularfehler |

## Kontrast

Hier unterscheiden sich alle drei Varianten, daher drei Spalten.

| Begriff | Bedeutung | Schlecht | Subtil | Gut |
| --- | --- | --- | --- | --- |
| **Normaler Text** | Mindestens **4,5 : 1** (WCAG AA). | ca. 2,3 : 1 – sichtbar blass | ca. 3,5 : 1 – wirkt okay, fällt durch | 14,7 : 1 |
| **Großer Text** | Ab 24 px oder ca. 18,7 px fett: mindestens **3 : 1**. | durchgefallen | Überschriften **bestehen** (3,3 : 1) | bestanden |
| **Nicht-Text-Kontrast** | Feldränder, Fokusrahmen, Icons: mindestens **3 : 1**. Von Tools oft nicht geprüft. | Feldrand kaum sichtbar | Feldrand 1,5 : 1, Fokus 1,3 : 1 | bestanden |
| **Farbe als einziges Merkmal** | Fehler nur rot zu markieren reicht nicht; immer Text ergänzen. | nur rote Umrandung | Rahmen + Fehlertext | Rahmen + Fehlertext |

---

## Navigation mit Tastatur und Screenreader

Wie Tab-Navigation, Fokusrahmen, Screenreader-Sprungtasten und der VoiceOver-Rotor praktisch funktionieren, steht in `HowToNavigate.md`.

---

## Fünf wichtige Ergänzungen (nicht in den Demos)

| Begriff | Bedeutung |
| --- | --- |
| `aria-live="polite"` / `"assertive"` | Allgemeine Form der Live-Region. `role="status"` entspricht `polite`; `assertive` unterbricht sofort. |
| `role="alert"` | Live-Region für dringende Meldungen (z. B. Fehler beim Absenden), wird sofort angesagt. |
| `tabindex="0"` | Macht ein eigenes Widget per `Tab` erreichbar. Positive Werte (`1`, `2` …) vermeiden. |
| `aria-current="page"` | Markiert den aktuellen Eintrag in der Navigation („aktuelle Seite“). |
| **Visually hidden** (`.sr-only`) | CSS-Klasse: Text visuell unsichtbar, für Screenreader aber lesbar – z. B. für Zusatzinfos an Icon-Buttons. |
