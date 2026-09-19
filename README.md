# Straußenburg-Portal – Vorgang 2026-0237

Digitale Modellstadt Straußenburg für die Ausbildung von Verwaltungsfachangestellten:
öffentliche Bürger-Website und internes Mitarbeiterportal mit digitaler Fallakte,
Aufbaumuster-Bearbeitung, Gutachtenerstellung und persönlichem Postfach.

## Projekt öffnen (lokal, zum Ansehen/Testen)

1. Den gesamten Ordner `Straußenburg-Portal/` (bzw. die ZIP-Datei) an die gewünschte
   Stelle entpacken/kopieren – z. B. auf den Schulrechner oder einen USB-Stick.
2. `straussenburg-portal.html` per Doppelklick im Browser öffnen (Chrome, Edge oder
   Firefox empfohlen). Es ist kein Server und keine Installation nötig.

**Wichtig:** Die Ordnerstruktur darf dabei nicht verändert werden. Die Datei
`straussenburg-portal.html` verlinkt die Vorgangsunterlagen über relative Pfade
(`akte/2026-0237/…`). Wird nur die HTML-Datei allein verschoben oder werden
Unterordner umbenannt, funktionieren die Dokumenten- und Bildlinks nicht mehr.

**Hinweis seit der Cloud-Anbindung:** Für die Anmeldung im Mitarbeiterportal ist
jetzt eine Internetverbindung nötig (siehe Abschnitt „Cloud-Speicherung" unten).
Nur lokal geöffnet, ohne Internetverbindung, funktioniert die öffentliche
Bürger-Website weiterhin uneingeschränkt, das Mitarbeiterportal-Login jedoch nicht.

## Online stellen (für den echten Einsatz mit der Klasse)

Die Datei ist weiterhin eine einzelne, eigenständige HTML-Datei ohne Server-
Backend – sie kann daher auf jedem beliebigen kostenlosen statischen Webspace
liegen (z. B. GitHub Pages, Netlify, oder der Webspace/IServ/Moodle der Schule),
solange **HTTPS** aktiv ist (für GitHub Pages und Netlify automatisch der Fall).

1. Den kompletten Ordner `Straußenburg-Portal/` (HTML-Datei + `akte/`-Unterordner)
   unverändert auf den Webspace hochladen.
2. In der Firebase-Konsole (siehe unten) unter „Authentication" → Tab
   „Settings" → „Authorized domains" die Domain hinzufügen, unter der die Seite
   künftig erreichbar ist (z. B. `ihrname.github.io`). Ohne diesen Eintrag
   verweigert Firebase die Anmeldung von dieser Domain aus.
3. Fertig – die URL kann an die Klasse weitergegeben werden.

## Cloud-Speicherung (Firebase) – geräteübergreifende Nutzung

Damit eine Person z. B. am Montag auf dem Schul-iPad und am Dienstag zu Hause
am Windows-Rechner an derselben Bearbeitung weiterarbeiten kann, speichert das
Portal die Eingaben, den Bearbeitungsstatus, das Gutachten und das Postfach
nicht mehr im Browser (`localStorage`), sondern in einer Firebase-Firestore-
Cloud-Datenbank. Die Website selbst bleibt weiterhin eine reine Client-
Anwendung – es gibt keinen eigenen Server, den Sie warten müssten.

**Wie das Login jetzt funktioniert:** Für die Schüler:innen ändert sich an der
Anmeldung nichts – weiterhin die ersten drei Buchstaben des Nachnamens und das
Passwort `1`. Im Hintergrund wird daraus beim allerersten Login automatisch ein
Firebase-Konto angelegt (mit einem intern erzeugten, für die Schüler:innen nie
sichtbaren technischen Passwort, unabhängig vom App-Passwort „1" – Firebase
verlangt mindestens 6 Zeichen). Über die dabei entstehende, nicht vorhersehbare
Firebase-Kennung wird der Datenzugriff in Firestore abgesichert (siehe
`firestore.rules`), sodass jede Person ausschließlich ihre eigenen Daten lesen
und schreiben kann.

**Bereits eingerichtet in diesem Paket:** Die Firebase-Projektkonfiguration
(Projekt „straussenburg") ist bereits in `straussenburg-portal.html` hinterlegt.
Damit die Cloud-Speicherung funktioniert, muss im zugehörigen Firebase-Projekt
einmalig Folgendes eingerichtet sein (in der Firebase-Konsole, console.firebase.google.com):

1. **Firestore Database** aktiviert (Modus „Produktion").
2. **Authentication** → Anbieter „E-Mail/Passwort" aktiviert.
3. Die **Sicherheitsregeln** aus der beiliegenden Datei `firestore.rules` unter
   Firestore Database → Tab „Regeln" eingefügt und veröffentlicht. Ohne diese
   Regeln kann entweder niemand oder – im ungünstigsten Fall – jede Person die
   Daten aller anderen lesen/verändern.
4. Falls die Seite online gestellt wird: die jeweilige Domain unter
   „Authorized domains" freigegeben (siehe Abschnitt „Online stellen" oben).

**Wichtige Einschränkung:** Ohne Internetverbindung ist im Mitarbeiterportal
weder ein Login noch das Speichern von Eingaben möglich (die öffentliche
Bürger-Website funktioniert weiterhin offline). Bei einer kurzzeitig
unterbrochenen Verbindung *während* der Bearbeitung bleiben bereits eingegebene
Texte lokal im Browser-Tab erhalten und werden automatisch nachgeliefert,
sobald wieder eine Verbindung besteht; am unteren Bildschirmrand erscheint in
diesem Fall ein Warnhinweis.

## Ordnerstruktur

```
Straußenburg-Portal/
├── straussenburg-portal.html      Portal (öffentliche Website + Mitarbeiterportal)
├── README.md                     diese Datei
└── akte/2026-0237/
    ├── dokumente/                Vorgangsunterlagen (PDF)
    └── bilder/                   Lichtbilder zur Fallakte (JPG)
```

## Login im Mitarbeiterportal

Über den Button „Mitarbeiterportal“ auf der Bürger-Website gelangt man zur Anmeldung.

- **Benutzername:** die ersten drei Buchstaben des Nachnamens (Groß-/Kleinschreibung
  spielt keine Rolle)
- **Passwort:** `1`

Beispiel: Max Müller meldet sich mit Benutzername `Mül` und Passwort `1` an.

**Hinweis (seit dem Online-Stellen):** Der Anmeldebildschirm selbst zeigt dieses
Schema nicht mehr an, damit Besucherinnen und Besucher der öffentlichen Seite
nicht auf das Anmeldeschema einer fremden Person schließen können. Benutzername
und Passwort müssen den Schülerinnen und Schülern daher separat (z. B. mündlich
oder auf einem Handout) mitgeteilt werden.

## Schülerliste ändern

Die tatsächliche Klassenliste trägt man direkt im Code ein. In
`straussenburg-portal.html` nach `SCHUELER_ROHDATEN` suchen (Abschnitt 1 der
Portal-Logik im `<script>`-Bereich) und dort Vorname/Nachname ergänzen oder
anpassen:

```js
const SCHUELER_ROHDATEN = [
  {vorname:"Max", nachname:"Müller"},
  {vorname:"Lisa", nachname:"Meyer"},
  // weitere Zeilen ergänzen
];
```

Benutzername und Passwort werden daraus automatisch gebildet, sofern nicht
abweichend angegeben.

## Speicherung des Bearbeitungsstands

Jede Person arbeitet in ihrem eigenen Browser. Eingaben, Bearbeitungsstatus,
Fortschritt, Gutachten und Postfach werden lokal im Browser gespeichert
(localStorage) – nicht auf einem Server.

- Die Daten bleiben auch nach dem Schließen des Browsers und nach einem
  Neustart des Rechners erhalten, solange derselbe Browser auf demselben
  Gerät verwendet wird und der Browserverlauf/Speicher nicht gelöscht wird.
- Die Daten verschiedener Schülerinnen und Schüler sind strikt getrennt:
  Person A sieht nie die Texte, den Fortschritt oder das Postfach von Person B.
- **Es gibt keine zentrale Synchronisation.** Arbeitet jemand an zwei
  verschiedenen Geräten (z. B. erst am Schulrechner, dann zu Hause), stehen
  dort jeweils unabhängige, eigene Datenstände zur Verfügung – nicht derselbe
  Bearbeitungsstand. Für eine Unterrichtseinheit empfiehlt sich daher pro
  Person durchgehend dasselbe Gerät.
- Ein „Testmodus“-Schalter in der Seitenleiste des Portals erlaubt es, für
  Testzwecke den eigenen Bearbeitungsstand zurückzusetzen oder Testabläufe zu
  simulieren, ohne die Daten anderer Nutzer zu beeinträchtigen.

## Pädagogisches Prinzip

Die Arbeitsblätter dienen der fachlichen Erarbeitung. Der Portalvorgang ist
eigenständig bearbeitbar: Sachverhalt, Verfahrensinformationen und Unterlagen
liegen direkt in der digitalen Fallakte vor.

Das Aufbaumuster bildet die Arbeitsstruktur ab. Reine Strukturüberschriften
besitzen kein Textfeld und lassen sich auf- und zuklappen. Dort, wo die
Mustergutachten einen eigenen ausformulierten Abschnitt enthalten, kann ein
eigener Text erfasst werden.

## Hinweis zu den Bildmaterialien

Die Bilddateien sind didaktische Bildsimulationen. Der Ausgangsfall
beschreibt einen dokumentierten Zaun, enthält in der vorliegenden Quelle aber
keine Originalfotografien. Die Simulationen machen die im Fall beschriebenen
örtlichen Merkmale im Portal sichtbar, ohne sie als echte Beweisfotos
auszugeben.

## Änderungsprotokoll (letzte Feinschliff-Runde)

1. **Namenskorrektur:** „Prabhjot Kau" → **„Prabhjot Kaur"** (Johal) und
   „Otlia" → **„Otilia"** (Seldüz) in `SCHUELER_ROHDATEN` korrigiert.
   *Designentscheidung:* Da die genaue Zielschreibweise nicht vorlag, wurde
   jeweils die im Deutschen/Punjabi gebräuchliche Standardform gewählt
   („Kaur" als verbreiteter Sikh-Namensbestandteil; „Otilia" als bekannter
   Vorname). Bitte prüfen und bei Bedarf korrigieren.
2. **Anmeldebildschirm gehärtet:** Der Hinweistext, der das genaue
   Anmeldeschema (erste drei Buchstaben des Nachnamens / Passwort „1")
   offenlegte, wurde vom Login-Bildschirm entfernt (Hintergrund: geplantes
   Online-Stellen der Seite). Das Schema selbst ist unverändert und bleibt
   hier im README dokumentiert.
3. **Neuer Aufbaumuster-Punkt:** Unter „B. I. Vorbehalt des Gesetzes", vor
   „1. Vorrangige besondere Regelung …", wurde der Punkt „Grundsatz der
   Gesetzmäßigkeit der Verwaltung" (eigenes Textfeld, Gewichtung 4) ergänzt.
   *Designentscheidung:* Da dieser neue Punkt keiner der bestehenden
   Nummern 1./2./3. zugeordnet werden sollte (um deren Nummerierung nicht
   zu verändern), wurde er unnummeriert (Aufzählungspunkt „•") vorangestellt.
   Die Gewichtung von 4 Punkten entspricht genau den 4 Punkten, die unter
   Punkt 4 (siehe unten) beim Prüfpunkt „Form" freigeworden sind – das
   Gesamtgewicht bleibt dadurch unverändert bei 100.
4. **Prüfpunkt „B. II. c. Form" vereinfacht:** Die beiden Unterpunkte
   (i. Formfreiheit, ii. Begründung bei schriftlicher Form) sowie das
   Textfeld wurden entfernt. Der Punkt erscheint jetzt nur noch als
   Anzeige-Hinweis „Muss nicht geprüft werden" (kein Textfeld, keine
   Statusanzeige, taucht mit diesem Hinweis auch im Gutachtentext auf).
   Die benachbarten Punkte „c) Bekanntgabe" und „d) Rechtsbehelfsbelehrung"
   wurden nicht verändert.

**Offene Punkte:** Bitte die beiden korrigierten Namen (Punkt 1) sowie die
Platzierung/Nummerierung/Gewichtung des neuen Punkts „Grundsatz der
Gesetzmäßigkeit der Verwaltung" (Punkt 3) gegenprüfen, falls die eigene
Aufbaumuster-Vorlage eine andere Nummerierung oder Gewichtung vorsieht.
