# Umfragetrend

Dieses Repository dient als Datenablage für das Umfragetrend-Tool. Alle Umfragen werden
von Hand über die Benutzeroberfläche eingetragen (kein automatisiertes Scraping) und
automatisch in die Datei [`umfragedaten.json`](./umfragedaten.json) synchronisiert.

## Einrichtung

1. **Repository vorbereiten**
   Leg dieses Repo an (privat oder öffentlich, wie du magst) und lade `umfragedaten.json`
   aus diesem Ordner als Startdatei hoch, oder lass sie beim ersten Verbinden automatisch
   vom Tool anlegen.

2. **Token erzeugen**
   Auf github.com unter *Settings → Developer settings → Personal access tokens →
   Fine-grained tokens* ein neues Token erstellen:
   - **Repository access:** nur dieses eine Repository auswählen
   - **Permissions:** *Contents* → *Read and write*
   - Keine weiteren Rechte vergeben

3. **Im Tool verbinden**
   Im Umfragetrend-Tool unter „GitHub-Speicherung":
   - Benutzer/Organisation, Repository-Name, Pfad (`umfragedaten.json`) und Branch (`main`) eintragen
   - Token einfügen
   - optional „Token merken" aktivieren, damit sich das Tool künftig automatisch verbindet
   - auf **Verbinden** klicken

Ab diesem Zeitpunkt wird jede Änderung im Tool automatisch in diese Datei geschrieben.

## Zwei getrennte Seiten: privat bearbeiten, öffentlich anzeigen

Im Repository liegen zwei unabhängige HTML-Seiten:

- **`index.html`** — deine private Eingabe-Oberfläche mit Formular, Einstellungen und
  GitHub-Verbindung samt Token. Diese Seite behältst du für dich.
- **`trend.html`** — eine rein lesende, öffentliche Ansicht: nur die Trendzahlen, der
  Chart und die Tabelle der berücksichtigten Umfragen. Kein Formular, keine Einstellungen,
  kein Token, keine Schreibmöglichkeit — sie liest `umfragedaten.json` direkt und ohne
  Anmeldung von GitHub (funktioniert bei einem öffentlichen Repository ganz ohne Token).
  **Diesen Link kannst du bedenkenlos z. B. in einem Substack-Post verlinken.**

Vor dem ersten Hochladen musst du in `trend.html` einmalig oben im Skript diese vier
Konstanten auf dein Repository anpassen:

```js
const GH_OWNER = "DEIN-GITHUB-BENUTZERNAME";
const GH_REPO = "DEIN-REPOSITORY";
const GH_PATH = "umfragedaten.json";
const GH_BRANCH = "main";
```

Danach beide Dateien (`index.html` und `trend.html`) zusammen mit `umfragedaten.json`
ins Repository hochladen — GitHub Pages liefert dann automatisch beide URLs aus:

```
https://<dein-benutzername>.github.io/<repo>/           → index.html (privat, mit Eingabe)
https://<dein-benutzername>.github.io/<repo>/trend.html → trend.html (öffentlich, nur lesend)
```

## Als eigenständige Seite betreiben (GitHub Pages)

Die Datei-basierte Synchronisierung per Browser-Fetch funktioniert **nicht** innerhalb von
claude.ai (dort blockiert die Artefakt-Sandbox Netzwerkaufrufe an Drittanbieter-APIs aus
Sicherheitsgründen). Läuft dieselbe Seite dagegen als ganz normale, eigenständige Webseite
außerhalb von claude.ai — zum Beispiel über GitHub Pages —, greift diese Beschränkung nicht
mehr, und die GitHub-Synchronisierung funktioniert normal.

### Schritt für Schritt

1. **Repository anlegen (falls noch nicht geschehen)**
   Auf github.com ein neues Repository erstellen, z. B. `umfragetrend` (privat oder
   öffentlich, beides funktioniert mit GitHub Pages).

2. **Dateien hochladen**
   Lade folgende Dateien aus diesem Ordner in das Repository hoch (per Drag-and-drop über
   „Add file → Upload files" auf der GitHub-Weboberfläche, oder per `git push`):
   - `index.html` — die private Eingabe-Anwendung
   - `trend.html` — die öffentliche, rein lesende Ansicht (Konstanten oben im Skript vorher anpassen, siehe unten)
   - `umfragedaten.json` — Startdatei mit leerer Umfrageliste

3. **GitHub Pages aktivieren**
   Im Repository zu *Settings → Pages* gehen:
   - **Source:** „Deploy from a branch"
   - **Branch:** `main`, Ordner `/ (root)`
   - Speichern. Nach ein bis zwei Minuten zeigt GitHub oben eine URL an, etwa
     `https://<dein-benutzername>.github.io/umfragetrend/`

4. **Seite öffnen und mit dem Repo verbinden**
   Die angezeigte URL öffnen — das ist jetzt die eigenständige, immer erreichbare Version
   des Tools. Im Abschnitt „GitHub-Speicherung":
   - Benutzer/Organisation, Repository, Pfad (`umfragedaten.json`) und Branch (`main`) eintragen
   - Das Fine-grained Token eintragen (siehe oben, Berechtigung *Contents: Read and write*,
     beschränkt auf genau dieses Repository)
   - Optional „Token merken" aktivieren — dann verbindet sich die Seite auf diesem Gerät
     künftig von selbst
   - Auf **Verbinden** klicken

Ab jetzt landet jede Änderung automatisch als Commit in `umfragedaten.json` in deinem
Repository — die Seite kannst du dir als Lesezeichen speichern und wie eine normale Web-App
nutzen, von jedem Gerät aus (Token ggf. erneut eintragen, falls nicht gemerkt).

### Hinweis zur Sicherheit

Auch außerhalb von claude.ai gilt: Nimm für den Personal Access Token unbedingt einen
**fine-grained Token, der ausschließlich auf dieses eine Repository beschränkt ist**. Die
„Token merken"-Option legt ihn nur im lokalen Speicher deines eigenen Browsers ab — nicht
auf einem gemeinsam genutzten oder öffentlichen Rechner aktivieren.

## Eigenen GitHub-Namen aus der URL heraushalten

Standardmäßig taucht dein persönlicher GitHub-Benutzername in der Pages-URL auf
(`https://<dein-name>.github.io/<repo>/`). Wer das nicht möchte, überträgt das Repository
auf eine kostenlose GitHub-Organisation mit frei wählbarem Namen — die URL enthält dann
nur noch diesen Organisationsnamen.

### 1. Neue Organisation anlegen

1. Oben rechts auf github.com auf **„+"** → **„New organization"**
2. Kostenlosen Plan („Free") wählen
3. Einen Organisationsnamen vergeben, der nichts mit dem persönlichen Benutzernamen zu tun
   hat (z. B. `umfragetrend-de`)
4. Beim Einladen von Mitgliedern ggf. „Skip this step" wählen

### 2. Bestehendes Repository übertragen

1. Ins Repository → **Settings**
2. Ganz nach unten zur **„Danger Zone"** scrollen
3. Bei **„Transfer ownership"** auf **Transfer** klicken
4. Als neuen Besitzer den Namen der neuen Organisation eintragen
5. Zur Bestätigung den angezeigten Repository-Namen eintippen
6. Auf **„I understand, transfer this repository"** klicken

Das Repo liegt danach unter `github.com/<organisation>/<repo>`. Der alte, persönliche Link
wird eine Zeit lang automatisch weitergeleitet — trotzdem Lesezeichen/Notizen aktualisieren.

### 3. GitHub Pages prüfen

Die Pages-Einstellungen (Branch, Ordner) wandern automatisch mit. Unter *Settings → Pages*
im übertragenen Repo erscheint die neue URL, z. B.:
```
https://<organisation>.github.io/<repo>/
```

### 4. `trend.html` (und `index.html`) anpassen

In `trend.html` die Konstante ganz oben ändern:
```js
const GH_OWNER = "<organisation>";   // statt des persönlichen Namens
```
Am einfachsten direkt auf github.com: Datei öffnen → Stift-Symbol („Edit this file") →
Zeile ändern → „Commit changes". Im GitHub-Bereich von `index.html` genügt es, beim
nächsten Verbinden im Feld „GitHub-Benutzer/-Organisation" den neuen Namen einzutragen.

### 5. Neues Token nötig

Ein Fine-grained Token, das für das persönliche Repo erstellt wurde, funktioniert nach der
Übertragung an eine Organisation in der Regel **nicht automatisch weiter** — Organisationen
verlangen für fine-grained Tokens meist eine explizite Freigabe durch den Org-Owner (bei
einer Solo-Organisation bist zwar du selbst der Owner, der Freigabeschritt bleibt aber
trotzdem nötig). Am einfachsten: ein neues Token erstellen (wie im Abschnitt „Einrichtung"
oben, beschränkt auf genau das übertragene Repository) und in `index.html`:

1. Falls „Token merken" aktiv war: erst auf **„Verbindung trennen"** klicken
2. Owner-Feld auf den Organisationsnamen ändern
3. Neues Token eintragen, wieder **„Verbinden"** klicken

### Weitere Optionen

- **Eigene Domain:** Unter *Settings → Pages → Custom domain* lässt sich eine eigene Domain
  hinterlegen (z. B. `trend.deinedomain.de`) — dann taucht gar kein GitHub-Name mehr in der
  sichtbaren Adresse auf.
- **Andere Hosting-Plattform** (Netlify, Vercel, Cloudflare Pages): `index.html`/`trend.html`
  lassen sich genauso dort mit einem frei wählbaren Projektnamen hosten. Wichtig zur
  Ehrlichkeit: `umfragedaten.json` liegt dabei weiterhin im GitHub-Repo, `trend.html` holt
  sie über `raw.githubusercontent.com/<owner>/...` — das steht zwar nicht in der Adresszeile,
  wäre aber bei einer Netzwerk-Analyse im Browser trotzdem sichtbar.

## Datenformat (`umfragedaten.json`)

```jsonc
{
  "parties": [
    { "id": "cdu", "name": "CDU/CSU", "color": "#B9BEC7", "active": true }
    // ... weitere Parteien
  ],
  "polls": [
    {
      "id": "eindeutige-id",
      "institute": "Forsa",
      "date": "2026-07-18",
      "values": { "cdu": 28.5, "spd": 15.0, "gruene": 12.0 }
    }
    // ... weitere Umfragen
  ],
  "instituteWeights": {
    "Forsa": 1.2
    // Institut -> frei gewählter Gewichtungsfaktor, 1.0 = neutral
  },
  "halfLifeDays": 21,
  "maxAgeDays": 365
}
```

- **parties**: Liste der Parteien mit Farbe und ob sie aktuell aktiv (also in Formular,
  Tabelle und Chart sichtbar) ist.
- **polls**: jede einzelne erfasste Umfrage mit Institut, Datum und den Prozentwerten je Partei.
- **instituteWeights**: der von Hand vergebene Gewichtungsfaktor je Institut. Werte über 1.0
  zählen stärker in den Trend, Werte darunter schwächer.
- **halfLifeDays**: Anzahl Tage, nach denen eine Umfrage die Hälfte ihres Gewichts verliert
  (exponentieller Zerfall, nähert sich Null nur asymptotisch an).
- **maxAgeDays**: harte Altersgrenze. Umfragen, die älter sind, zählen mit Gewicht 0 —
  unabhängig vom exponentiellen Zerfall. Das verhindert, dass sehr alte Umfragen (z. B. von
  vor einem Jahr) rechnerisch noch einen (wenn auch winzigen) Einfluss behalten.

## Versionierung

Da die Datei bei jeder Änderung per GitHub-API committet wird, entsteht automatisch eine
Historie aller bisherigen Stände über den Commit-Verlauf dieser Datei — praktisch, um
frühere Trendstände nachzuvollziehen oder eine Änderung rückgängig zu machen.
