# Textspur — Handbuch

Textspur verwandelt Aufzeichnungen in lesbaren Text. Alles läuft auf dem eigenen
Rechner, ohne Cloud und ohne Konto.

Dieses Handbuch beschreibt Version 1.2.1.

---

## Inhalt

1. [Was Textspur tut](#1-was-textspur-tut)
2. [Installation](#2-installation)
3. [Der erste Lauf](#3-der-erste-lauf)
4. [Der Arbeitsablauf](#4-der-arbeitsablauf)
5. [Die Tabs im Einzelnen](#5-die-tabs-im-einzelnen)
6. [Einstellungen](#6-einstellungen)
7. [Was am Ende herauskommt](#7-was-am-ende-herauskommt)
8. [Sprecher erkennen und benennen](#8-sprecher-erkennen-und-benennen)
9. [Erkennung verbessern: die Begriffsliste](#9-erkennung-verbessern-die-begriffsliste)
10. [Bildschirm-Inhalte aus Videos](#10-bildschirm-inhalte-aus-videos)
11. [Übersetzung](#11-übersetzung)
12. [Wo Textspur seine Daten ablegt](#12-wo-textspur-seine-daten-ablegt)
13. [Wenn etwas nicht klappt](#13-wenn-etwas-nicht-klappt)

---

## 1. Was Textspur tut

Textspur nimmt Audio- und Videodateien und erzeugt daraus ein Transkript. Die
Spracherkennung übernimmt [WhisperX](https://github.com/m-bain/whisperX), das
lokal auf der CPU läuft. Es gehen keine Daten ins Netz.

Was dabei entsteht, geht über reinen Text hinaus:

- **Wer hat wann gesprochen.** Auf Wunsch trennt Textspur die Sprecher und lässt
  Namen vergeben.
- **Zeitstempel bis auf das einzelne Wort.** Daraus entstehen Untertitel, und im
  Sprecher-Tab lässt sich jede Stelle direkt anhören.
- **Bildschirm-Inhalte.** Bei Videokonferenzen findet Textspur die gezeigten
  Folien und legt sie zeitlich einsortiert ins Dokument.
- **Übersetzungen**, falls ein passender Dienst hinterlegt ist.

**Was Textspur nicht ist:** kein Echtzeit-Mitschnitt und keine Aufnahme-Software.
Es verarbeitet fertige Dateien.

### Wie lange dauert das

Auf einer gewöhnlichen Büro-CPU braucht ein Lauf ungefähr so lange wie die
Aufnahme selbst, gemessen etwa das 1,1-fache mit Sprechertrennung. Eine Stunde
Aufzeichnung ist also nach gut einer Stunde fertig. Ein Grafikprozessor wird
nicht benötigt und auch nicht genutzt.

---

## 2. Installation

### Textspur selbst

Den Installer gibt es unter
[Releases](https://github.com/Montinew/textspur/releases/latest) als
`Textspur-<Version>-Setup.exe`. Er installiert pro Benutzer nach
`%LocalAppData%\Programs\Textspur` und braucht **keine Administratorrechte**.

Der Installer ist nicht code-signiert. Windows SmartScreen warnt deshalb beim
ersten Start. Über „Weitere Informationen" und „Trotzdem ausführen" geht es
weiter. Wer sichergehen will, prüft die Prüfsumme aus dem Release:

```
certutil -hashfile Textspur-<Version>-Setup.exe SHA256
```

### Was Textspur zusätzlich braucht

| Werkzeug | Wofür | Wie es dazukommt |
|---|---|---|
| ffmpeg | Ton lesen, Bilder extrahieren | Der Installer bietet die Installation über winget an |
| WhisperX | die eigentliche Spracherkennung | Textspur richtet es beim ersten Lauf selbst ein |
| Hugging-Face-Token | Sprechertrennung | kostenlos, siehe unten |
| Tesseract | Text auf erfassten Folien lesen | optional, `winget install UB-Mannheim.TesseractOCR` |

**ffmpeg** ist die einzige Voraussetzung, die wirklich vorhanden sein muss. Die
Voraussetzungsseite des Installers prüft das und bietet an, es per winget
nachzuinstallieren. Wer es lieber selbst verwaltet, legt es in den `PATH` oder
trägt den vollen Pfad in den Einstellungen ein.

**WhisperX** muss man nicht selbst installieren. Beim ersten Lauf fragt Textspur,
ob es eine eigene Umgebung einrichten soll, und lädt dann Python 3.12 samt
WhisperX nach `%LocalAppData%\Textspur\runtime`. Das ist ein einmaliger Download
von knapp einem Gigabyte. Wer bereits ein Python mit WhisperX hat, trägt dessen
Pfad in den Einstellungen ein und überspringt das.

---

## 3. Der erste Lauf

1. Textspur starten.
2. Im Tab **Quelle & Ausgabe** auf **Dateien wählen** und eine Aufnahme
   auswählen.
3. Oben in der Werkzeugleiste auf **▶ Transkription starten**.

Beim allerersten Mal passiert danach einiges im Hintergrund. Textspur richtet die
WhisperX-Umgebung ein und lädt das Sprachmodell herunter, zusammen etwa zwei
Gigabyte. Das Live-Log unten im Fenster zeigt, was gerade läuft. Ab dem zweiten
Lauf entfällt das.

Ist der Lauf fertig, liegt das Transkript im Ausgabeverzeichnis, und der Tab
**Transkript** zeigt den Text.

### Sprechertrennung einschalten

Ohne Sprechertrennung bekommt das ganze Transkript einen einzigen Sammelsprecher.
Für die Trennung braucht es einen kostenlosen Zugang bei Hugging Face:

1. Konto auf [huggingface.co](https://huggingface.co) anlegen.
2. Auf der Seite
   [pyannote/speaker-diarization-community-1](https://huggingface.co/pyannote/speaker-diarization-community-1)
   die Nutzungsbedingungen bestätigen.
3. Unter Einstellungen einen Zugriffs-Token mit Leserechten erzeugen.
4. Den Token in Textspur unter **⚙ Einstellungen** eintragen und die
   Diarisierung aktivieren.

---

## 4. Der Arbeitsablauf

Textspur ist um eine Tabelle herum gebaut. Jede Zeile ist eine Aufnahme, und in
der Zeile steht alles, was für diese Aufnahme gilt.

### Einzeln oder zusammengefasst

Rechts über der Tabelle sitzt der Schalter **Batch-Modus**.

**Batch ist der Standard und der Normalfall.** Jede Aufnahme wird zu einem
eigenen Transkript mit eigenem Ordner und eigenem Namen. Jede fertige Datei wird
sofort grün hinterlegt und steht im Sprecher-Tab zur Bearbeitung bereit, während
die übrigen noch laufen. Fällt eine Datei aus, laufen die übrigen weiter; die
gescheiterte bleibt rot markiert in der Liste stehen.

**Zusammenfassen** ist die Ausnahme. Alle Aufnahmen werden zu **einem**
Transkript verkettet, sinnvoll etwa bei einer Sitzung, die in mehreren Dateien
aufgezeichnet wurde. Dann gilt die **erste Zeile** für den ganzen Lauf: ihr
Export-Name, ihre Optionen. Die übrigen Zeilen sind gesperrt, und genau daran
erkennt man, in welchem Modus man ist.

### Was in einer Zeile steht

| Spalte | Bedeutung |
|---|---|
| Datei | Der Dateiname. Grün hinterlegt mit Haken: fertig transkribiert und im Sprecher-Tab bearbeitbar. Ein rotes Warnzeichen bedeutet, dass diese Datei beim letzten Lauf gescheitert ist; der Tooltip nennt den Grund. |
| Export-Name | Der Basisname für Ordner und Dateien. Leer heißt: der Dateiname wird genommen. |
| Länge, Format, Größe | Aus der Datei ausgelesen. Steht dort „n/a", fehlt ffprobe. |
| Audio | Nur die Tonspur behalten. **Die einzige Aktion, die etwas löscht.** |
| Kopie | Das Original am Platz lassen statt es in den Job-Ordner zu verschieben. |
| Folien | Präsentierte Bildschirm-Inhalte aus dem Video erfassen. |

### Was mit der Quelldatei geschieht

Das ist wichtig genug für einen eigenen Absatz, denn hier werden Dateien bewegt.

**Standard:** Nach erfolgreicher Transkription wird die Aufnahme in den
Job-Ordner **verschoben**. Sie liegt danach beim Transkript und nicht mehr am
Ursprungsort. Das hält zusammen, was zusammengehört.

**Kopie:** Das Original bleibt, wo es war, und wandert zusätzlich in den
Job-Ordner.

**Audio:** Bei Videos wird nach der Transkription eine Tonspur extrahiert und das
Video **gelöscht**. Das ist die einzige löschende Aktion in Textspur. Sie greift
erst, wenn die Tonspur nachweislich geschrieben wurde, sie ist pro Datei zu
wählen, und vor dem Start nennt eine Rückfrage die betroffenen Dateien
namentlich. Nach jedem Lauf springt die Einstellung auf den sicheren Standard
zurück.

---

## 5. Die Tabs im Einzelnen

### Quelle & Ausgabe

Oben die Tabelle, in der Mitte eine Voransicht mit eingebautem Abspieler, unten
das Live-Log. Das Log lässt sich ausblenden; die Einstellung wird gemerkt. Bei
Problemen ist es die erste Anlaufstelle, denn dort steht auch, mit welchen
Einstellungen der Lauf gestartet wurde.

Die Reihenfolge der Zeilen lässt sich mit den Pfeiltasten ändern. Beim
Zusammenfassen bestimmt sie, in welcher Folge die Aufnahmen aneinandergehängt
werden.

### Transkript

Der fertige Text, nach Sprechern gegliedert, mit Suche. Ein Doppelklick auf eine
Zeile springt im Abspieler an die passende Stelle.

### Sprecher

Hier bekommen die erkannten Stimmen ihre Namen. Ausführlich in Abschnitt 8.

### Folien

Die aus dem Video erfassten Bildschirm-Inhalte als Kachelraster zum Abhaken.
Ausführlich in Abschnitt 10.

### Begriffe

Fachwörter und Abkürzungen, die Textspur beim nächsten Lauf kennen soll.
Ausführlich in Abschnitt 9.

---

## 6. Einstellungen

Über **⚙ Einstellungen** oben rechts. Alles hier gilt global, also für alle
Aufnahmen. Was pro Aufnahme unterschiedlich sein kann, steht in der Tabelle.

**Ausgabe-Verzeichnis** — wo die Job-Ordner entstehen.

**Modellqualität** — von `tiny` bis `large-v3`. `large-v3` ist deutlich genauer
und deutlich langsamer. Für ernsthafte Arbeit ist es die richtige Wahl; die
kleinen Modelle taugen zum Ausprobieren.

**Quellsprache** — eine feste Sprache ist schneller und zuverlässiger als `auto`.
Bei `auto` erkennt Whisper die Sprache selbst, was Zeit kostet und in seltenen
Fällen danebengeht.

**Stapelgröße** — wie viele Audio-Abschnitte gleichzeitig verarbeitet werden. Auf
der CPU bringt ein höherer Wert kaum Tempo, kostet aber Speicher. Gemessen an
zwei Minuten Audio mit `large-v3`:

| Stapelgröße | Speicherspitze | Dauer |
|---|---|---|
| 8 (Standard) | 4837 MB | 115 s |
| 4 | 4357 MB | 119 s |
| 1 | 3562 MB | 123 s |

Auf einem knappen Rechner lohnt das Heruntergehen. Bricht ein Lauf mit einer
Speichermeldung ab, wiederholt Textspur die Datei ohnehin einmal automatisch mit
Stapelgröße 1.

**Formate** — welche Dateien geschrieben werden: Untertitel als SRT und das
Dokument als HTML lassen sich abschalten, JSON und TXT entstehen immer.

**Diarisierung** — Sprechertrennung ein oder aus, dazu der Hugging-Face-Token.

**Übersetzung** — siehe Abschnitt 11.

**Samples pro Sprecher** — wie viele Hörproben der Sprecher-Tab je Stimme
anbietet.

**Werkzeug-Pfade** — Python, ffmpeg und Tesseract, falls sie nicht im `PATH`
liegen. Daneben der Knopf, mit dem Textspur seine eigene WhisperX-Umgebung
einrichtet.

**Wort-Alignment** gibt es als Einstellung nicht mehr. Es läuft immer, weil es
gemessen keine Zeit kostet, am erkannten Text nichts ändert und die Grundlage für
Wort-Zeitstempel, saubere Satzgrenzen und die Sprecher-Korrektur ist.

---

## 7. Was am Ende herauskommt

Je Aufnahme entsteht ein Ordner, benannt nach dem Export-Namen und einem
Zeitstempel:

```
<Ausgabe-Verzeichnis>\
  Teammeeting_20260904_105034\
    Teammeeting.normalized.json     <- das Transkript, die maßgebliche Datei
    Teammeeting.txt                 <- Fließtext nach Sprechern
    Teammeeting.srt                 <- Untertitel
    Teammeeting.html                <- lesbares Dokument mit Folien
    screens\                        <- die erfassten Bildschirm-Inhalte
    01-of-01_Aufnahme_20260904_105034\
      Aufnahme.mkv                  <- die Quelldatei
      Aufnahme.json                 <- die Rohausgabe von WhisperX
```

**Die JSON-Datei ist das Original.** Sie enthält alles: Text, Zeitstempel je Wort,
Sprecher, Folien, Übersetzungen. Die übrigen Dateien werden daraus abgeleitet und
lassen sich jederzeit neu erzeugen.

**Das HTML-Dokument** bringt eigene Druckregeln mit. Wer ein PDF braucht, öffnet
es im Browser und druckt es dorthin. Ein eigener PDF-Export wäre eine zusätzliche
Fremdbibliothek für ein Ergebnis, das jeder Browser ohnehin liefert.

**Der Ordner ist in sich geschlossen und darf verschoben werden.** Die Verweise
auf die Quelldatei sind relativ. Auch ältere Transkripte mit absoluten Pfaden
finden ihre Aufnahme wieder, solange sie im selben Ordner liegt.

### Re-Export

Der Knopf **⤴ Re-Export** in der Werkzeugleiste, auch über Strg+S erreichbar,
schreibt TXT, SRT und HTML neu aus dem aktuellen Stand. Wurde der Export-Name
geändert, benennt der Re-Export auch die Dateien und den Job-Ordner um. Danach
steht einige Sekunden lang ein grüner Hinweis mit Uhrzeit, Name und Ordner neben
dem Knopf. Der Re-Export geht auch während eines Batch-Laufs, für jede Datei, die
schon fertig ist.

---

## 8. Sprecher erkennen und benennen

Ist die Diarisierung aktiv, trennt Textspur die Stimmen und nennt sie zunächst
`Sprecher_1`, `Sprecher_2` und so weiter. Der Sprecher-Tab dient dazu, daraus
Namen zu machen.

Je Stimme bietet Textspur einige Hörproben an. Sie werden so ausgewählt, dass sie
etwas taugen: mindestens zwei Sekunden echte Sprechzeit, ein halber Sekunde
Abstand zu fremden Stimmen, mindestens dreißig Sekunden auseinander. Stellen, an
denen sich zwei Sprecher die Zeit teilen, kommen erst zum Zug, wenn die
eindeutigen nicht reichen.

**So geht es am schnellsten:** die erste Probe anhören, den Namen ins Feld
tippen, zur nächsten Stimme. Der Name wird sofort gespeichert, es gibt keinen
Speichern-Knopf.

### Wenn eine Zuordnung nicht stimmt

Ein Doppelklick auf eine Probenkarte öffnet den Grenzen-Editor. Dort lassen sich
Anfang und Ende eines Abschnitts ziehen; mit gedrückter Strg-Taste rastet die
Marke auf Zehntelsekunden ein.

Textspur korrigiert die Sprecher-Zuordnung beim Laden bereits selbst. WhisperX
vergibt das Sprecher-Label nach zeitlicher Überlappung, wodurch ein einzelnes
schlecht vermessenes Wort ein ganzes Segment auf die falsche Stimme kippen kann.
Textspur zieht das Label stattdessen aus der Mehrheit der Wörter nach. An einem
echten Transkript mit 287 Segmenten hat das elf Zuordnungen richtiggestellt.

### Mehrere Transkripte gleichzeitig

Im Batch landet jedes Ergebnis im Umschalter oben im Sprecher-Tab, sobald die
Datei fertig ist, nicht erst am Ende des Laufs. Man kann also die ersten Sprecher
benennen, während die nächsten Aufnahmen noch transkribiert werden.
Daneben steht das Feld für den Export-Namen. Das ist der einzige Ort, an dem sich
der Name eines bereits erzeugten Transkripts ändern lässt.

---

## 9. Erkennung verbessern: die Begriffsliste

Fachwörter, Abkürzungen und englische Fachbegriffe sind die typischen
Stolperstellen. Die Begriffsliste im Tab **Begriffe** sagt WhisperX beim nächsten
Lauf, welche Wörter es erwarten darf.

Der Weg dorthin ist rückwärts gedacht, und das ist Absicht. Eine Liste im Voraus
zu pflegen ist nicht zu leisten, denn man weiß vorher nicht, woran das Modell
scheitern wird. Beim Korrekturlesen fällt es dagegen sofort auf.

**Der Ablauf:**

1. Transkript in Textspur laden.
2. Die zugehörige TXT-Datei in einem Texteditor öffnen, Korrektur lesen,
   speichern.
3. Im Tab Begriffe auf **📄 Korrigierte Textdatei abgleichen** und die Datei
   wählen.

Textspur vergleicht die Datei mit dem geladenen Transkript, sammelt die
brauchbaren Wörter in die Liste und bietet an, die korrigierten Sätze ins
Transkript zu übernehmen.

**Alltagswörter werden dabei aussortiert.** Wenn „aber" gesagt und „oder"
verstanden wurde, ist das ein Hörfehler und keine Vokabel. Solche Korrekturen
landen nicht in der Liste, sondern rechts in der Übersicht der verworfenen
Vorschläge, jeweils mit Begründung. Umgekehrt kommen Abkürzungen, Wörter mit
Großschreibung im Inneren und alles mit Ziffern immer durch.

**Bekannte Fehlerkennungen werden nach jedem Lauf ersetzt.** Die Liste ist für
das Modell nur ein Hinweis, keine Garantie: Bei gleich klingenden Namen wie
„Wibke“ und „Wiebke“ entscheidet es von Lauf zu Lauf anders. Steht bei einem
Begriff in der Spalte „Erkannt als“ die falsche Schreibweise, ersetzt Textspur sie
deshalb nach dem Lauf im ganzen Transkript, bevor die Dateien geschrieben werden.
Ersetzt werden nur ganze Wörter, unabhängig von Groß- und Kleinschreibung; das
Live-Log nennt jede Ersetzung. Ist die falsche Schreibweise ein Alltagswort oder
kürzer als drei Buchstaben, ersetzt Textspur sie nicht, weil sonst auch richtige
Stellen überschrieben würden. Auch das steht im Log.

Jeder Eintrag lässt sich abwählen, dann bleibt er gespeichert, geht aber nicht in
den Lauf und wird auch nicht ersetzt. Oder löschen.

**Der Platz im Modell ist begrenzt.** Passen nicht alle Begriffe hinein, gehen
die mit den meisten Treffern zuerst mit. Textspur zählt nach jedem Lauf, welche
Begriffe tatsächlich vorkamen. Was gesprochen wird, bleibt vorn; was nie
auftaucht, rückt nach hinten, bleibt aber gespeichert.

---

## 10. Bildschirm-Inhalte aus Videos

Bei aufgezeichneten Videokonferenzen geht das Gezeigte im Text verloren. Die
Spalte **Folien** in der Quelltabelle holt es zurück: Textspur findet die
Passagen, in denen etwas präsentiert wird, und legt Screenshots davon an.

Die Erkennung sucht nach **Stillstand**, nicht nach Szenenwechseln. Eine Folie
steht still, ein Mensch nie. Innerhalb einer Präsentationsphase markieren dann
Ausschläge im Bild die Folienwechsel.

**Die Erkennung ist auf Vollständigkeit ausgelegt und nimmt Fehltreffer in
Kauf.** Leere Bildschirme, Kameraansichten und Hintergrundbilder sind normal. Die
Auswahl trifft der Mensch: im Tab **Folien** liegen alle Funde als Kacheln zum
Abhaken. Ein Doppelklick öffnet die Fundstelle im Video in einem eigenen Fenster.
Nur abgehakte Bilder landen im HTML-Dokument.

Ist Tesseract installiert, liest Textspur zusätzlich den Text der Bilder. Das
hilft beim Sortieren: echte Folien ergeben zusammenhängende Sätze, Fehltreffer
nur Zeichensalat, und die Textvorschau steht in der Kachel.

Die Erfassung läuft parallel zur Transkription und kostet deshalb kaum
zusätzliche Zeit. Der Knopf **Bildschirm-Inhalte jetzt erfassen** rüstet sie für
ein bereits fertiges Transkript nach, ohne die Spracherkennung zu wiederholen.

---

## 11. Übersetzung

Textspur kann das fertige Transkript übersetzen lassen. Das ist der einzige
Schritt, bei dem Daten den Rechner verlassen, und er ist standardmäßig aus.

Nötig sind ein OpenAI-kompatibler Endpunkt für Chat-Completions, ein Modellname
und ein Schlüssel. Das kann ein Dienst im Netz sein oder ein lokal betriebener
Server. Zielsprachen lassen sich mehrere gleichzeitig wählen; je Sprache entsteht
ein eigener Satz Ausgabedateien.

---

## 12. Wo Textspur seine Daten ablegt

Alles unter `%LocalAppData%\Textspur`:

| Pfad | Inhalt |
|---|---|
| `settings.json` | die Einstellungen |
| `vocabulary.json` | die Begriffsliste |
| `session.log` | das Protokoll der laufenden Sitzung, bei jedem Start neu |
| `crash.log` | falls Textspur einmal abstürzt |
| `runtime\` | die selbst eingerichtete WhisperX-Umgebung und die Modelle |
| `tessdata\` | eigene Sprachdateien für Tesseract |

Der Ordner `runtime` wird groß, meist mehrere Gigabyte. Dort liegen die
Sprachmodelle. Er lässt sich gefahrlos löschen; Textspur lädt beim nächsten Lauf
neu, was es braucht.

Die Transkripte selbst liegen **nicht** hier, sondern im eingestellten
Ausgabe-Verzeichnis.

---

## 13. Wenn etwas nicht klappt

**Erste Anlaufstelle ist das Live-Log** unten im Tab Quelle & Ausgabe. Vor jeder
Datei steht dort eine Zeile mit den verwendeten Einstellungen. Dieselben Zeilen
stehen in `%LocalAppData%\Textspur\session.log`, allerdings nur für die laufende
Sitzung; ein Neustart überschreibt die Datei.

### Häufige Fälle

**„ffmpeg wurde nicht gefunden"** — ffmpeg fehlt oder liegt nicht im `PATH`.
Entweder `winget install Gyan.FFmpeg` ausführen oder den vollen Pfad in den
Einstellungen eintragen. Textspur bricht bewusst sofort ab, statt erst nach
minutenlangem Modell-Download zu scheitern.

**„Keine funktionierende WhisperX-Umgebung"** — den Einrichtungsknopf in den
Einstellungen verwenden oder den Pfad zu einem passenden Python eintragen.

**Die Datei hat keine Tonspur** — Textspur prüft das vorher und lehnt ab. Ohne
diese Prüfung liefe WhisperX durch und lieferte ein leeres Transkript.

**Ein Abbruch mit einer Speichermeldung** — Textspur wiederholt die Datei
automatisch einmal mit Stapelgröße 1. Passiert es öfter, die Stapelgröße dauerhaft
auf 4 oder 1 stellen.

**Alle Sprecher heißen gleich** — die Diarisierung war aus oder der
Hugging-Face-Token hat keinen Zugriff auf das pyannote-Modell. Die Bedingungen
auf der Modellseite müssen bestätigt sein.

**Die Videos werden im Sprecher-Tab nicht gefunden** — passiert, wenn die
Quelldatei nach dem Lauf von Hand woandershin verschoben wurde. Liegt sie noch im
Job-Ordner, findet Textspur sie auch nach einem Umzug des ganzen Ordners.

**Kein Text auf den Folien** — Tesseract fehlt. Ohne Tesseract entstehen die
Bilder trotzdem, nur eben ohne erkannten Text. Für Deutsch braucht es zusätzlich
`deu.traineddata`; der Windows-Installer bringt nur Englisch mit. Die Datei kann
ohne Administratorrechte unter `%LocalAppData%\Textspur\tessdata\` abgelegt
werden.

### Fehler melden

Als [Issue](https://github.com/Montinew/textspur/issues), bitte mit der
Textspur-Version aus dem Info-Fenster, der Windows-Version und dem
Abschnitt aus dem Log. Der Log enthält keinen Hugging-Face-Token, er lässt sich
also gefahrlos weitergeben.

---

Textspur ist Freeware von [David Bachmann](https://david-bachmann.de).
