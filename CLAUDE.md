# CLAUDE.md – Dataviz Review Tool

## Projektübersicht

Ein interaktives Workshop-Tool zur **qualitativen Bewertung von Datenvisualisierungen**.
Zielgruppe: Nicht-Profis die mit Agenturen zusammenarbeiten und Visualisierungen in Auftrag geben oder abnehmen.

Das Tool wird von der Moderatorin auf dem eigenen Laptop (Beamer) bedient  
und per Link auch auf privaten Geräten der Teilnehmer zugänglich gemacht.

---

## Tech Stack

- **Plain HTML/CSS/JS** – single file, kein Build-Step, kein Framework
- **Anthropic API** (`claude-sonnet-4-20250514`) für KI-Bewertungen
- **API Key** wird als Konstante oben in der JS-Sektion konfiguriert (kein .env nötig)
- **Deployment**: GitHub Pages oder Netlify (statisches Hosting)
- **Sprache der UI**: Deutsch

---

## Zwei Kernflows

### Flow 1 – Chart-Selector: „Welches Diagramm passt?"

Nutzer beschreibt:
1. Was sind meine Daten? (Kategorien, Zeitreihe, Verhältnisse, Verteilung, Zusammenhang…)
2. Was will ich zeigen/beweisen?
3. Für welches Publikum?

→ Output: Empfehlung mit Begründung + Warnung bei häufigen Fehlern

### Flow 2 – Chart-Review: „Ist diese Visualisierung gut?"

Nutzer beschreibt (oder lädt Screenshot hoch):
1. Was soll diese Viz zeigen?
2. Beschreibung oder Upload der Viz

→ Output: Strukturiertes Feedback nach Bewertungsrahmen (siehe unten)

---

## Bewertungsrahmen (Wissensbasis für System Prompt)

### Alberto Cairo – 5 Qualitätsdimensionen („How Charts Lie", 2019)
Primärer Framework. Cairo schreibt explizit für Viz-Konsumenten, nicht Ersteller.

1. **Truthful** – Zeigt es die Daten korrekt und ohne Verzerrung?
2. **Functional** – Erfüllt es seinen kommunikativen Zweck?
3. **Beautiful** – Ist es ästhetisch angemessen für den Kontext?
4. **Insightful** – Zeigt es etwas das man nicht sofort sieht?
5. **Enlightening** – Hat es gesellschaftliche/kommunikative Relevanz?

Im Tool-Kontext: Fokus auf **Truthful** und **Functional** – die anderen als Bonuspunkte.

### Darrell Huff – Manipulationsmuster („How to Lie with Statistics", 1954)
Checkliste für den „Manipulations-Radar":

- Abgeschnittene Y-Achse (beginnt nicht bei 0 ohne Kennzeichnung)
- Fehlende Grundgesamtheit (n=?)
- Irreführende Durchschnitte (Mean vs. Median)
- Zwei Y-Achsen mit unterschiedlicher Skalierung
- Tortendiagramme mit zu vielen Segmenten (>5)
- Fehlender Zeitraum oder fehlende Quelle
- 3D-Effekte die Proportionen verzerren
- Cherry-picking: Zeitraum wurde günstig gewählt

### Cleveland & McGill (1984) / Heer & Bostock (2010)
Für Chart-Empfehlungen: Wahrnehmungsgenauigkeit nach Encoding-Typ:
Position > Länge > Winkel/Steigung > Fläche > Farbe/Sättigung

Praktische Konsequenz:
- Balken > Torten (Länge > Winkel)
- Scatter > Bubble Charts (Position > Fläche)
- Line Chart > Area Chart wenn absolute Werte zählen

---

## System Prompt Logik

Alle API-Calls bekommen diesen System-Kontext:

```
Du bist ein Experte für Datenvisualisierung und hilfst Menschen die keine 
Statistiker sind, Diagramme und Charts kritisch zu beurteilen.

Deine Antworten basieren auf:
- Alberto Cairos fünf Qualitätsdimensionen (Truthful, Functional, Beautiful, Insightful, Enlightening)
- Darrell Huffs Manipulationsmustern aus "How to Lie with Statistics"
- Wahrnehmungsforschung von Cleveland & McGill (1984)

Schreibe immer auf Deutsch. Vermeide Fachjargon.
Strukturiere Feedback mit maximal 3 konkreten Punkten.
Sei direkt aber konstruktiv – die Nutzer sollen Agenturen briefen können,
nicht beschämen.
Wenn du unsicher bist weil zu wenig Information vorliegt, frage nach.
```

---

## UI / UX Anforderungen

- **Beamer-optimiert**: Große Schrift (min. 18px body), hoher Kontrast
- **Zwei klar getrennte Tabs/Sektionen**: Chart-Selector | Chart-Review
- **Case-Beschreibungsfeld** in beiden Flows prominent
- **Ergebnis-Bereich** groß und gut lesbar – wird auf Beamer gezeigt
- **Ladeindikator** während API-Call
- **Design**: Clean, professionell – kein Comic-Look, kein Corporate-Blau
- Optional: Ergebnis kopieren / Screenshot-Button für Teilnehmer

---

## Dateistruktur

```
dataviz-tool/
├── index.html        ← gesamte App (HTML + CSS + JS in einer Datei)
├── CLAUDE.md         ← diese Datei
└── README.md         ← Deployment-Anleitung
```

---

## Entwicklungsreihenfolge (für Claude Code)

1. **Grundstruktur** – HTML Skeleton, zwei Tabs, Eingabefelder, API Key Config
2. **Flow 1** – Chart-Selector mit API Integration
3. **Flow 2** – Chart-Review mit API Integration, Text-Beschreibung + Bild-Upload (JPG/PNG als base64 an Vision API)
4. **UI Polish** – Beamer-Optimierung, Ladestate, Fehlerhandling
5. **Deployment** – README mit GitHub Pages Anleitung

---

## Was Claude Code NICHT tun soll

- Kein React, kein Build-Tool, kein npm
- Keine externen UI-Libraries (kein Bootstrap, kein Tailwind CDN-Abhängigkeit)
- Keine Datenbank, kein Backend
- Keine Nutzerdaten speichern
- Keinen API Key ins Git-Repository committen (Kommentar-Platzhalter reicht)

---

## Offene Entscheidungen

- [ ] Figma-Design vor UI-Implementation? (Empfehlung: ja, nach Flow-Validierung)
- [x] Bild-Upload für Chart-Review ist Teil von V1 – Screenshot/JPG/PNG Upload direkt im Review-Flow, wird als base64 an die Anthropic API übergeben (Vision-fähiges Modell)
- [ ] Workshop-spezifischer API Key mit Rate Limit konfigurieren
