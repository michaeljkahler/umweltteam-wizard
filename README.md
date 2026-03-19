# 🌿 Umweltteam-Wizard

**[→ Zum Tool / Open Tool](https://michaeljkahler.github.io/umweltteam-wizard/wizard/umweltteam-wizard.html)**

**Interaktives Planungs- und Projektmanagement-Tool für Pfadi-Umweltteams in Kantonalverbänden.**

Ein standalone HTML-Tool, das Kantonalverbände der Pfadibewegung Schweiz dabei unterstützt, ihr Umweltteam aufzubauen, Angebote zu planen und Projekte professionell zu managen – ohne Login, ohne Server, ohne Installation.

> 🇨🇭 Zweisprachig: Deutsch / Français

---

## Übersicht

| Feature | Beschreibung |
|---------|-------------|
| **Wizard-Flow** | 11-Schritte-Assistent von der Standortbestimmung bis zum Projektexport |
| **Multi-Projekt** | Mehrere Projekte parallel verwalten mit eigenem Gantt, Budget und Aufgaben |
| **Gantt-Chart** | Wochenbasierte Zeitplanung (KW 1–52) mit Verantwortlichkeiten und Farbcodierung |
| **Budget-Request** | Dynamische Budgettabelle mit Live-Summe und Antragsformular |
| **Aufgaben-Tracker** | Aufgabenliste mit Status-Badges (Offen / In Arbeit / Erledigt) |
| **Persistenz** | JSON Export/Import – jederzeit speichern und später weiterarbeiten |
| **Export** | PDF, DOCX und iCal (.ics) Export pro Projekt oder gesamthaft |
| **Offline-fähig** | Funktioniert nach dem ersten Laden komplett ohne Internet |

---

## Projektstruktur

```
umwelt/
├── wizard/
│   └── umweltteam-wizard.html    # Hauptanwendung (Single-File HTML)
├── workshop/
│   ├── Workshop-Umweltteam-Wahlblock.pdf   # Druckfertiger Workshop-Leitfaden
│   ├── ablauf/                    # Workshop-Ablaufpläne
│   └── materialien/               # Handouts, Flipchart-Vorlagen
├── tool/
│   └── assets/                    # Bilder, Logos, Icons
├── export/
│   └── vorlagen/                  # Export-Templates
├── recherche/                     # Quellen, Hintergrundmaterial
└── README.md
```

---

## Wizard-Flow (11 Schritte)

```
┌─────────────────────────────────────────────────────────┐
│  1. Info          KV-Name, Kontakt, Pfadiname           │
│  2. Standort      Neustart / Aufbau / Etabliert         │
│  3. Angebote      11 Kategorien + Freitext auswählen    │
│  4. Ressourcen    Personen, Budget, Infrastruktur        │
│  5. Jahresplan    12-Monats-Raster mit Drag & Drop      │
│ ─── Projektmanagement ──────────────────────────────── │
│  6. Projekte      Übersicht, anlegen, auswählen         │
│  7. Beschrieb     Ziel, Zielgruppe, Scope, Risiken      │
│  8. Budget        Dynamische Tabelle + Antrag            │
│  9. Gantt         Wochenbasiert mit Verantwortlichen     │
│ 10. Aufgaben      Wer macht was bis wann                 │
│ 11. Export        PDF / DOCX / iCal – einzeln oder alle │
└─────────────────────────────────────────────────────────┘
```

---

## Technologie

| Komponente | Technologie |
|-----------|-------------|
| Frontend | Vanilla HTML/CSS/JS (Single File) |
| PDF-Export | [html2pdf.js](https://github.com/eKoopmans/html2pdf.js) v0.10.1 via CDN |
| DOCX-Export | [docx](https://github.com/dolanmiu/docx) v8.5.0 via CDN |
| iCal-Export | Native JS (VCALENDAR/VEVENT-Generierung) |
| Persistenz | JSON Serialisierung (Download/Upload) |
| Hosting | Statisch – keine Serverkomponente nötig |

### CDN-Abhängigkeiten

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/docx/8.5.0/docx.min.js"></script>
```

---

## Installation & Nutzung

### Lokal

```bash
git clone https://github.com/<org>/umweltteam-wizard.git
cd umweltteam-wizard
# Einfach die HTML-Datei im Browser öffnen:
open wizard/umweltteam-wizard.html
```

Kein Build-Schritt, kein Node.js, kein Server nötig.

### GitHub Pages

1. Repository Settings → Pages → Source: `main` branch, `/` (root)
2. Die Seite ist erreichbar unter `https://<org>.github.io/umweltteam-wizard/wizard/umweltteam-wizard.html`

### Eigener Webserver

Die Datei `wizard/umweltteam-wizard.html` kann auf jedem statischen Webserver gehostet werden (Apache, Nginx, Netlify, Vercel, etc.).

---

## Features im Detail

### Multi-Projekt-Verwaltung

Jeder KV kann beliebig viele Projekte anlegen (z.B. «Pfadiheim-Check», «Lager-Leitfaden», «Clean-Up-Day»). Jedes Projekt hat eigene Daten für Beschrieb, Budget, Gantt und Aufgaben. Ein Projekt-Selektor ist auf den PM-Schritten (7–10) immer sichtbar.

### Gantt-Chart

- 52-Wochen-Timeline mit Monatsgruppierung
- Verantwortliche Person pro Aufgabe
- Farbcodierung nach Person (automatisch aus 8-Farben-Palette)
- Horizontal scrollbar bei vielen Wochen
- iCal-Export für Outlook / Google Calendar / Apple Calendar

### Persistenz (Speichern/Laden)

```
💾 Speichern  →  umweltteam-{KV-Name}-backup.json
📂 Laden      →  JSON-Datei auswählen, Daten werden wiederhergestellt
```

Das JSON enthält alle KV-Daten und alle Projekte. Ideal für:
- Gemeinsames Arbeiten (JSON per Mail/Chat teilen)
- Backup vor dem Workshop
- Weiterarbeit an einem anderen Gerät

### Export-Formate

| Format | Inhalt | Trigger |
|--------|--------|---------|
| **PDF** | Vollständiger Bericht mit allen Sektionen, Tabellen, Gantt | html2pdf.js → Download |
| **DOCX** | Strukturiertes Word-Dokument mit Headings und Tabellen | docx.js → Download |
| **iCal** | Gantt-Aufgaben als Kalendereinträge (.ics) | Native JS → Download |
| **JSON** | Kompletter Datenstand für Backup/Weitergabe | JSON.stringify → Download |

---

## Angebotspalette (vordefinierte Kategorien)

Die folgenden Angebote stehen im Wizard zur Auswahl:

1. **Kursinhalte erarbeiten** – Umwelt-Blöcke für alle Kursstufen
2. **Modul Fortbildung** – Weiterbildung für Leitende
3. **Grundlagen & Merkblätter** – Factsheets, Checklisten, Leitfäden
4. **Aktivitätssammlung** – Fix-fertige Umwelt-Aktivitäten nach Stufe
5. **Auskunftsstelle** – Ansprechstelle für Fragen aus Abteilungen
6. **Pfadiheim-Check** – Audit: Recycling, Energie, Abfall, Umgebung
7. **Lagerleitfäden** – Nachhaltiges Lagern, «Faires Lager»
8. **Kantonale Umwelt-Events** – Clean-Ups, Naturschutz-Aktionen
9. **Vernetzung & Austausch** – Best Practices teilen, Erfa-Treffen
10. **Umwelt-Lobbying im KV** – Richtlinien, Gremienarbeit
11. **J+S / «Faires Lager»** – Zusammenarbeit mit PBS-Projekten

---

## Workshop-Leitfaden

Im Ordner `workshop/` liegt ein druckfertiger PDF-Leitfaden (6 Seiten) für den physischen Workshop «Wahlblock Umweltteam». Inhalt:

- Ablauf-Übersicht (45–60 Min, 7 Phasen)
- Detaillierter Ablauf mit Leitfragen (DE + FR)
- Angebotstabelle zweisprachig
- Hintergrundwissen für Moderierende
- Leeres Arbeitsblatt zum Ausfüllen

---

## Zielgruppe

- **Primär:** 20–30-jährige Pfadileiter\*innen aus verschiedenen Berufen und Hintergründen, die ehrenamtlich ein Umweltteam im Kantonalverband aufbauen oder betreiben
- **Sekundär:** KV-Leitungen, die den Aufbau von Umweltteams unterstützen möchten
- **Kontext:** Pfadibewegung Schweiz (PBS), Kantonalverbände (KVs), Umweltteams

---

## Weiterentwicklung

### Geplant

- [ ] Cloud-Sync (optional) für geteilte Projektarbeit
- [ ] Mehrsprachigkeit erweitern (IT, EN)
- [ ] Vorlagen-Bibliothek für häufige Projekte
- [ ] Druckoptimierte Gantt-Ansicht
- [ ] PWA-Support für vollständige Offline-Nutzung

### Contributing

1. Fork erstellen
2. Feature-Branch: `git checkout -b feature/mein-feature`
3. Änderungen committen: `git commit -m 'Add: Mein Feature'`
4. Branch pushen: `git push origin feature/mein-feature`
5. Pull Request öffnen

Da es sich um eine Single-File-Anwendung handelt, bitte darauf achten, dass alle Änderungen in `wizard/umweltteam-wizard.html` bleiben (kein Build-System).

---

## Lizenz

[MIT](LICENSE) – Frei verwendbar für alle Pfadiorganisationen und Jugendverbände.

---

## Kontakt

Pfadibewegung Schweiz – Umweltteam PBS
