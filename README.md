# Dateimanager-App

Eine Android-Dateimanager-App zum Verwalten von Dateien innerhalb eines app-internen Verzeichnisses. Die Anwendung wurde mit **Ionic**, **Vue 3**, **TypeScript** und **Capacitor** umgesetzt.

## Umgesetzte Funktionen

Die App erfüllt die grundlegenden Anforderungen der Aufgabenstellung:

* [x] Die App unterstützt Android.
* [x] Es existiert eine Listenansicht, die alle Dateien und Ordner, die in der App im aktuellen Verzeichnis gespeichert sind, auflistet.
* [x] Die Listenansicht besitzt einen Button, über den neue Dateien oder Ordner zur App hinzugefügt werden können.
* [x] Beim Hinzufügen neuer Dateien müssen diese durch die App in ein internes Verzeichnis der App kopiert werden.
* [x] Das Hinzufügen eines neuen Ordners erfordert mindestens die Eingabe eines Namens.
* [x] Jede Datei kann (ggf. mit anderen Apps) geöffnet werden.
* [x] Jeder Ordner kann geöffnet werden, indem eine Navigation in diesen Ordner stattfindet.
* [x] Jede Datei und jeder Ordner kann gelöscht werden.
* [x] Das Löschen eines Ordners oder einer Datei muss durch den Nutzer über einen Dialog bestätigt werden.
* [x] In der Listenansicht sollte sichtbar werden, ob es sich bei einem Listeneintrag um eine Datei oder einen Ordner handelt.

## Technischer Aufbau

### Verwendete Technologien

| Technologie      | Verwendung                                                          |
| ---------------- | ------------------------------------------------------------------- |
| Vue 3            | Aufbau und Zustandsverwaltung der Benutzeroberfläche                |
| TypeScript       | Programmiersprache der Web-App                                      |
| Ionic Vue        | Mobile UI-Komponenten wie Listen, Buttons, Alerts und Action Sheets |
| Capacitor 8      | Verbindung zwischen Web-App und nativer Android-Plattform           |
| Android / Gradle | Native Android-Anwendung und APK-Build                              |

### Verwendete Capacitor-Plugins

#### `@capacitor/filesystem`

Das Filesystem-Plugin übernimmt die gesamte Dateiverwaltung der App:

* Auslesen von Verzeichnissen (`readdir`)
* Erstellen von Ordnern (`mkdir`)
* Speichern importierter Dateien (`writeFile`)
* Erzeugen von URIs (`getUri`)
* Löschen von Dateien (`deleteFile`)
* Löschen von Ordnern (`rmdir`)

Alle Daten werden innerhalb des App-internen Verzeichnisses gespeichert:

```text
Directory.Data/dateimanager-files/
```

Damit werden ausschließlich selbst importierte Inhalte verwaltet.

#### `@capawesome/capacitor-file-picker`

Der File Picker wird zum Importieren von Dateien verwendet:

```ts
FilePicker.pickFiles({
  readData: true,
  limit: 1,
})
```

Die Datei wird direkt als Base64 eingelesen und anschließend in das interne Verzeichnis der App geschrieben.

#### `@capawesome-team/capacitor-file-opener`

Zum Öffnen von Dateien wird eine URI erzeugt und an das native System übergeben:

```ts
await FileOpener.openFile({ path: uri });
```

Android öffnet die Datei anschließend mit einer passenden installierten App.

## Projektstruktur

```text
dateimanager-app/
├── android/
├── public/
│   └── favicon.png
├── src/
│   ├── router/
│   ├── theme/
│   ├── views/
│   │   └── HomePage.vue
│   ├── App.vue
│   └── main.ts
├── .env.example
├── capacitor.config.ts
├── ionic.config.json
├── package.json
├── tsconfig.json
└── vite.config.ts
```

Die zentrale Logik befindet sich in:

```text
src/views/HomePage.vue
```

## Voraussetzungen

Für einen einfachen Funktionstest der App ist keine Entwicklungsumgebung notwendig. Die mitgelieferte APK-Datei kann direkt auf einem Android-Gerät installiert werden.

Soll die App aus dem Quellcode gestartet oder neu gebaut werden, werden folgende Programme benötigt:

* Node.js inklusive npm
* Android Studio
* Android SDK
* JDK 21

Das Projekt enthält bereits die native Android-Plattform im Ordner `android/`.

Die App ist für folgende Android-Versionen konfiguriert:

```text
minSdkVersion:     24
compileSdkVersion: 36
targetSdkVersion:  36
```

Damit wird **Android 7.0 (API-Level 24) oder höher** unterstützt.

> Android Studio bringt in der Regel bereits ein geeignetes JDK mit. Eine separate Java-Installation ist daher normalerweise nicht notwendig.

## App testen

### Möglichkeit 1: APK direkt installieren

Dies ist der einfachste Weg, die App zu testen.

Benötigt werden:

* ein Android-Gerät mit Android 7.0 oder neuer
* die mitgelieferte APK-Datei unter `apk/Dateimanager.apk` ([Dateimanager.apk](./apk/Dateimanager.apk))

Vorgehen:

1. Die APK-Datei auf das Android-Gerät übertragen.
2. Die APK-Datei auf dem Gerät öffnen.
3. Falls Android die Installation blockiert, die Installation von Apps aus dieser Quelle erlauben.
4. Die Installation bestätigen.
5. Die App anschließend über das App-Menü starten.

## App aus dem Quellcode starten

### 1. Abhängigkeiten installieren

Nach dem Herunterladen bzw. Entpacken des Projekts im Hauptverzeichnis ein Terminal öffnen und ausführen:

```bash
npm install
```

Dadurch werden alle benötigten Abhängigkeiten wie Vue, Ionic, Capacitor und die verwendeten Plugins installiert.

### 2. Web-Anwendung bauen

```bash
npm run build
```

Dadurch wird die Web-Anwendung gebaut und im Ordner `dist/` abgelegt.

### 3. Android-Projekt synchronisieren

```bash
npx cap sync android
```

Dadurch wird der aktuelle Web-Build in das native Android-Projekt kopiert. Gleichzeitig werden die installierten Capacitor-Plugins mit dem Android-Projekt synchronisiert.

### 4. Android-Projekt öffnen

```bash
npx cap open android
```

Dadurch wird das Projekt in Android Studio geöffnet.

### 5. App starten

In Android Studio kann die App entweder auf

* einem angeschlossenen Android-Gerät oder
* einem Android-Emulator

ausgeführt werden.

Dazu in Android Studio das gewünschte Gerät auswählen und anschließend auf **Run** klicken.

## APK erstellen

Die APK kann direkt über Android Studio erstellt werden:

1. Android-Projekt öffnen:

   ```bash
   npx cap open android
   ```

2. In Android Studio **Build → Build Bundle(s) / APK(s) → Build APK(s)** auswählen.

Alternativ kann die Debug-APK über Gradle erstellt werden.

```powershell
cd android
.\gradlew.bat assembleDebug
```

Die erzeugte Debug-APK befindet sich anschließend normalerweise unter:

```text
android/app/build/outputs/apk/debug/app-debug.apk
```

## KI-Nutzung

Im Rahmen der Entwicklung wurden verschiedene KI-Tools unterstützend eingesetzt.

### ChatGPT 5.6

Verwendet für:

* Unterstützung bei allgemeinen Programmierfragen
* Hilfe bei der Fehlersuche
* Erklärungen zu verwendeten Technologien und Plugins
* Unterstützung bei der Dokumentation

### Claude Sonnet 5

Verwendet für:

* Unterstützung bei der Strukturierung des Codes
* Vorschläge zur Verbesserung einzelner Codeabschnitte
* Hilfe bei der Fehlersuche

### ChatGPT Codex 5.6 Sol

Verwendet für:

* Unterstützung bei der Implementierung einzelner Funktionen
* Generierung und Anpassung von Code
* Analyse vorhandener Codeabschnitte
* Unterstützung bei kleineren Fehlerbehebungen und Verbesserungen
