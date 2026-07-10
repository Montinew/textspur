# Textspur

Textspur transkribiert Audio- und Video-Dateien zu Text — komplett lokal auf dem eigenen Rechner mit [WhisperX](https://github.com/m-bain/whisperX), ohne Cloud und ohne Konto. Auf Wunsch mit Sprecher-Erkennung (Diarisierung) und nachgelagerter Übersetzung. Export als TXT, SRT und JSON.

Textspur ist ein Werkzeug von [David Bachmann](https://david-bachmann.de).

## Download

Die aktuelle Version gibt es unter [Releases](https://github.com/Montinew/textspur/releases/latest) als Windows-Installer (`Textspur-<Version>-Setup.exe`). Der Installer ist self-contained (.NET-Runtime und LibVLC enthalten) und installiert pro Benutzer ohne Admin-Rechte.

Zu jedem Release gehört eine `SHA256SUMS.txt`. Der Installer ist nicht code-signiert; Windows SmartScreen zeigt beim ersten Start deshalb eine Warnung („Weitere Informationen" → „Trotzdem ausführen"). Die Echtheit lässt sich über die Checksum prüfen:

```
certutil -hashfile Textspur-<Version>-Setup.exe SHA256
```

## Voraussetzungen

Textspur startet externe Werkzeuge, die separat installiert sein müssen:

- **Python ≥ 3.12** mit installiertem [`whisperx`](https://github.com/m-bain/whisperX) (inklusive `torch`/`torchaudio`)
- **`ffmpeg`** im `PATH` (empfohlen inklusive `ffprobe` und `ffplay`); alternativ lassen sich volle Pfade in den App-Einstellungen hinterlegen
- **Optional** für die Sprecher-Erkennung: ein [Hugging-Face-Token](https://huggingface.co/pyannote/speaker-diarization-community-1) mit Zugriff auf das pyannote-Modell
- **Optional** für die Übersetzung: ein OpenAI-kompatibler Chat-Completions-Endpoint samt API-Key

## Fehler und Wünsche

Fehlermeldungen und Feature-Wünsche gerne als [Issue](https://github.com/Montinew/textspur/issues) — bitte mit Textspur-Version, Windows-Version und einer kurzen Beschreibung, was passiert ist.

## Lizenz und Quellcode

Textspur ist Freeware — kostenlos nutzbar, Details in [LICENSE.txt](LICENSE.txt). Der Quellcode ist derzeit nicht veröffentlicht; dieses Repository dient der Verteilung der Releases und als Anlaufstelle für Issues.
