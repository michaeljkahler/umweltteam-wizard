# Umweltteam-Wizard – Projektkontext für Claude Code

## Projektziel

Interaktives HTML-Tool für Pfadi-Umweltteams in Schweizer Kantonalverbänden. Unterstützt beim Aufbau, der Angebotsplanung und dem Projektmanagement. Standalone Single-File HTML, zweisprachig DE/FR, kein Server nötig.

## Architektur

**Single-File HTML-Anwendung** (`wizard/umweltteam-wizard.html`, ~2400 Zeilen)

Alles inline: HTML-Struktur, CSS-Styles, JavaScript-Logik. Keine Build-Pipeline, kein Framework.

### Externe Abhängigkeiten (CDN)

```
html2pdf.js 0.10.1 → PDF-Export
docx 8.5.0         → DOCX-Export
```

### Datenmodell

Zentrales `data`-Objekt im JS-Scope:

```javascript
data = {
  // KV-Grunddaten (Steps 1-5)
  kvName, pfadiName, teamName, contact, email, teamSize,
  status,          // 'neustart' | 'aufbau' | 'etabliert'
  challenges,
  offerings: [],   // Array von Offering-Keys (z.B. 'cat_kurse')
  ownIdea,
  people, hours, budget, infra, support,
  plan: ['', '', ... x12],  // Jahresplan pro Monat

  // Projekte (Steps 6-10)
  currentProjectIdx: 0,
  projects: [{
    id, name,
    beschrieb: { projektName, projektZiel, zielgruppe, umfang, deliverables, risiken, kriterien },
    budget: { rows: [{posten, beschreibung, betrag}], antragsteller, budgetDatum, genehmigung },
    gantt: [{ name, verantwortlich, start (KW 1-52), end (KW 1-52) }],
    aufgaben: [{ aufgabe, verantwortlich, deadline, status }]
  }]
}
```

### Rendering-Pattern

Jeder Step hat eine `renderStepN(el)` Funktion die `el.innerHTML` setzt. Navigation über `nav(dir)` ruft `saveCurrentStep()` → `renderStep()` auf.

```
renderStep() → switch(currentStep) → renderStep1..11(main)
saveCurrentStep() → liest DOM-Inputs → schreibt in data-Objekt
```

### i18n

Zweisprachig über `i18n`-Objekt mit `de` und `fr` Keys. `t(key)` gibt den übersetzten String zurück. `setLang(l)` wechselt die Sprache und rendert neu.

## Dateistruktur

```
umwelt/
├── wizard/
│   └── umweltteam-wizard.html     # Hauptanwendung
├── workshop/
│   ├── Workshop-Umweltteam-Wahlblock.pdf  # Generiert via workshop_pdf.py
│   ├── ablauf/
│   └── materialien/
├── tool/assets/
├── export/vorlagen/
├── recherche/
├── README.md                      # GitHub README
└── CLAUDE.md                      # Diese Datei
```

## Wizard-Flow (11 Steps)

| Step | Funktion | Daten-Scope |
|------|----------|-------------|
| 0 | Info (KV, Kontakt, Pfadiname) | `data.*` |
| 1 | Standortbestimmung | `data.status, data.challenges` |
| 2 | Angebotspalette (11 Kategorien) | `data.offerings[]` |
| 3 | Ressourcen & Budget | `data.people, hours, budget...` |
| 4 | Jahresplan (12 Monate, Drag&Drop) | `data.plan[]` |
| 5 | Projektübersicht (Multi-Projekt) | `data.projects[], data.currentProjectIdx` |
| 6 | Projektbeschrieb | `project.beschrieb.*` |
| 7 | Budget-Request | `project.budget.*` |
| 8 | Gantt-Chart (Wochen, KW 1-52) | `project.gantt[]` |
| 9 | Aufgaben-Tracker | `project.aufgaben[]` |
| 10 | Zusammenfassung & Export | Read-only, Export-Logik |

## Schlüsselfunktionen

### Multi-Projekt

`getCurrentProject()` gibt das aktive Projekt zurück. Projekt-Selektor-Bar auf Steps 6-9 erlaubt Wechsel ohne Navigation. `createEmptyProject(name)` erzeugt ein leeres Projekt-Objekt.

### Gantt-Chart

Wochenbasiert (KW 1-52). `renderGanttVisual(tasks)` erzeugt CSS-Grid mit `grid-template-columns: repeat(52, minmax(16px, 1fr))`. Balken via `grid-column: start / end+1`. Farben pro verantwortliche Person aus `colorPalette[]`.

### Budget

Live-Summenberechnung über `updateBudgetTotal()` mit `oninput`-Handler auf Betrags-Inputs. `saveBudgetRows()` synchronisiert DOM → Data.

### Persistenz

`exportData()` → `JSON.stringify(data)` → Download als `.json`
`importData(event)` → FileReader → `JSON.parse` → `Object.assign(data, imported)` → re-render

### Export

- **PDF**: `exportPDF()` → `generateExportContent()` erzeugt styled HTML → `html2pdf().from(content).save()`
- **DOCX**: `exportWord()` → `docx.Document` mit Headings, Paragraphs, Tables → `Packer.toBlob()`
- **iCal**: `exportICalendar()` → VCALENDAR/VEVENT String → Blob Download als `.ics`

## Design-System

```css
--green-dark:  #2d6a4f   /* Header, Titles */
--green-mid:   #40916c   /* Buttons, Headings */
--green-light: #52b788   /* Hover, Accents */
--green-pale:  #b7e4c7   /* Borders, Backgrounds */
--green-bg:    #d8f3dc   /* Light backgrounds */
--accent:      #f77f00   /* CTAs, Orange highlights */
```

Cards mit `border-radius: 12px`, `box-shadow: 0 2px 12px rgba(0,0,0,0.08)`. Font: Segoe UI / system-ui.

## Offering-Keys (i18n)

```
cat_kurse, cat_fortbildung, cat_merkblatt, cat_aktivitaeten,
cat_auskunft, cat_heimcheck, cat_lager, cat_events,
cat_vernetzung, cat_lobby, cat_j_s
```

Jeder Key hat `_d` Suffix für die Beschreibung (z.B. `cat_kurse_d`).

## Workshop-PDF

Generiert via `workshop_pdf.py` mit `reportlab`. Output: `workshop/Workshop-Umweltteam-Wahlblock.pdf` (6 Seiten A4). Zweisprachig DE/FR. Custom Page-Templates für Cover und Folgeseiten.

## Bekannte Einschränkungen

- Kein Cloud-Sync (nur lokaler JSON Export/Import)
- CDN-Libs brauchen Internet beim ersten Laden
- Gantt hat keine Abhängigkeiten zwischen Aufgaben
- DOCX-Export ist strukturiert aber ohne komplexes Styling (kein Gantt-Bild im DOCX)
- PDF-Export via html2pdf kann bei sehr langen Berichten Seitenumbrüche suboptimal setzen

## Konventionen

- Alle neuen Features müssen DE + FR Übersetzungen im `i18n`-Objekt haben
- Keine externen Dateien – alles bleibt in der einen HTML-Datei
- CSS-Variablen aus dem Design-System verwenden
- `saveCurrentStep()` muss bei neuen Steps erweitert werden
- Neue Projekt-Datenfelder in `createEmptyProject()` hinzufügen

## Zielgruppe

20–30-jährige Pfadileiter*innen, ehrenamtlich, verschiedene berufliche Hintergründe, brauchen einfache PM-Hilfe ohne Vorkenntnisse. UI muss freundlich, intuitiv und nicht zu „corporate" sein.
