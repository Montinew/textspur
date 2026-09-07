# Textspur

Textspur transkribiert Audio- und Video-Dateien zu Text — komplett lokal auf dem eigenen Rechner mit [WhisperX](https://github.com/m-bain/whisperX), ohne Cloud und ohne Konto. Auf Wunsch mit Sprecher-Erkennung (Diarisierung), nachgelagerter Übersetzung und Erfassung präsentierter Bildschirm-Inhalte (Folien) aus Videos. Export als TXT, SRT, JSON und HTML.

Textspur ist ein Werkzeug von [David Bachmann](https://david-bachmann.de).

**➜ [Das Handbuch](HANDBUCH.md)** beschreibt Installation, Bedienung und alle Funktionen.

## Was Textspur kann

- **Transkribieren** — Audio und Video zu Text, lokal auf der CPU. Eine Stunde Aufnahme braucht ungefähr eine Stunde.
- **Sprecher trennen und benennen** — mit Hörproben je Stimme, damit die Zuordnung nicht geraten werden muss.
- **Untertitel und Dokument** — TXT, SRT, JSON und ein druckbares HTML-Dokument.
- **Folien aus Videos** — bei Videokonferenzen findet Textspur die gezeigten Bildschirm-Inhalte und sortiert sie zeitlich ins Dokument ein.
- **Dazulernen** — Fachbegriffe und Abkürzungen, die einmal korrigiert wurden, kennt Textspur beim nächsten Lauf.
- **Stapelverarbeitung** — beliebig viele Aufnahmen nacheinander; scheitert eine, laufen die übrigen weiter.
- **Übersetzen** — optional über einen OpenAI-kompatiblen Endpunkt. Der einzige Schritt, bei dem Daten den Rechner verlassen, standardmäßig aus.

## Download

Die aktuelle Version gibt es unter [Releases](https://github.com/Montinew/textspur/releases/latest) als Windows-Installer (`Textspur-<Version>-Setup.exe`). Der Installer ist self-contained (.NET-Runtime und LibVLC enthalten) und installiert pro Benutzer ohne Admin-Rechte.

Zu jedem Release gehört eine `SHA256SUMS.txt`. Der Installer ist nicht code-signiert; Windows SmartScreen zeigt beim ersten Start deshalb eine Warnung („Weitere Informationen" → „Trotzdem ausführen"). Die Echtheit lässt sich über die Checksum prüfen:

```
certutil -hashfile Textspur-<Version>-Setup.exe SHA256
```

## Voraussetzungen

Textspur startet externe Werkzeuge:

- **WhisperX (Python)** — muss nicht mehr selbst installiert werden: Textspur richtet beim ersten Lauf auf Nachfrage eine eigene Umgebung ein (Python 3.12 + `whisperx` unter `%LocalAppData%\Textspur\runtime`, einmaliger Download unter 1 GB). Eine vorhandene Installation lässt sich weiterhin über den Python-Pfad in den Einstellungen nutzen.
- **`ffmpeg`** (mit `ffprobe` und `ffplay`) — der Installer bietet an, es über `winget` mitzuinstallieren; alternativ im `PATH` bereitstellen oder volle Pfade in den App-Einstellungen hinterlegen
- **Optional** für die Sprecher-Erkennung: ein [Hugging-Face-Token](https://huggingface.co/pyannote/speaker-diarization-community-1) mit Zugriff auf das pyannote-Modell
- **Optional** für die Übersetzung: ein OpenAI-kompatibler Chat-Completions-Endpoint samt API-Key
- **Optional** für durchsuchbaren Text auf erfassten Folien: [Tesseract OCR](https://github.com/UB-Mannheim/tesseract/wiki) (`winget install UB-Mannheim.TesseractOCR`); ohne Tesseract entstehen die Screenshots trotzdem

Beim ersten Lauf lädt Textspur zusätzlich das Sprachmodell herunter, zusammen mit der WhisperX-Umgebung rund zwei Gigabyte. Ab dem zweiten Lauf entfällt das.

## Fehler und Wünsche

Fehlermeldungen und Feature-Wünsche gerne als [Issue](https://github.com/Montinew/textspur/issues) — bitte mit Textspur-Version (steht im Info-Fenster), Windows-Version und einer kurzen Beschreibung, was passiert ist.

Hilfreich ist der Abschnitt aus dem Protokoll unter `%LocalAppData%\Textspur\session.log`. Es enthält keinen Hugging-Face-Token und lässt sich gefahrlos weitergeben.

## Lizenz und Quellcode

Textspur ist Freeware — kostenlos nutzbar, Details in [LICENSE.txt](LICENSE.txt). Der Quellcode ist derzeit nicht veröffentlicht; dieses Repository dient der Verteilung der Releases und als Anlaufstelle für Issues.
