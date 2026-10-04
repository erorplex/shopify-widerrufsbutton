# Widerrufsbutton für Shopify

Seit dem 19. Juni 2026 brauchen Onlineshops, die an Verbraucher verkaufen, eine elektronische
Widerrufsfunktion: den Button „Vertrag widerrufen“ (§ 356a BGB). Diese Anleitung baut ihn in
Shopify nur mit Bordmitteln und kostenlosen Apps, ohne Programmieren und ohne eigenen Server.
Rechne mit etwa einer Stunde.

> **Keine Rechtsberatung.** Die Anleitung beschreibt eine technische Umsetzung, die wir so in
> eigenen Shops betreiben. Ob sie für deinen Shop genügt, klärst du im Zweifel mit einer Anwältin
> oder einem Anwalt. Ohne Gewähr.

## Was das Gesetz verlangt und wie es hier gelöst ist

| § 356a BGB | Umsetzung |
|---|---|
| Abs. 1: Funktion „Vertrag widerrufen“, während der Widerrufsfrist ständig verfügbar, hervorgehoben, leicht zugänglich | Button in der Fußzeile jeder Seite, als Schaltfläche abgesetzt, ohne Anmeldung; dazu ein Menüpunkt im Kundenkonto |
| Abs. 2: Name, Angaben zum Vertrag oder zum widerrufenen Teil, E-Mail für die Bestätigung | Formular mit drei Pflichtfeldern und einem freiwilligen Feld für einzelne Artikel |
| Abs. 3: Bestätigungsfunktion „Widerruf bestätigen“ | So heißt die Absende-Schaltfläche |
| Abs. 4: unverzüglich eine Eingangsbestätigung auf dauerhaftem Datenträger mit Inhalt, Datum und Uhrzeit | Automatische E-Mail innerhalb von Sekunden, Kopie an dein Postfach |
| Abs. 5: rechtzeitig, wenn vor Fristende abgeschickt | Der Eingang wird mit Zeitpunkt gespeichert und in der Mail genannt |

Außerdem muss deine Widerrufsbelehrung die Funktion nennen. Fehlt das, beginnt die Widerrufsfrist
nicht zu laufen. Textvorschläge: [vorlagen/rechtstexte.md](vorlagen/rechtstexte.md).

## So funktioniert es

1. Der Kunde klickt in der Fußzeile auf „Vertrag widerrufen“.
2. Er landet auf `/pages/vertrag-widerrufen`, einem Formular aus **Shopify Forms**.
3. Er füllt es aus und klickt „Widerruf bestätigen“. Forms speichert die Einsendung.
4. **Shopify Flow** bemerkt die neue Einsendung und startet die App **Workflow Transactional
   Email**. Die schickt die Bestätigung über dein eigenes Postfach an den Kunden, mit Kopie an dich.

## Was du brauchst

| | Kosten |
|---|---|
| Shopify-Shop, jeder Tarif (auch Basic) | – |
| [Shopify Forms](https://apps.shopify.com/shopify-forms) (von Shopify) | kostenlos |
| [Shopify Flow](https://apps.shopify.com/flow) (von Shopify) | kostenlos |
| [Workflow Transactional Email](https://apps.shopify.com/flow-action-extensions) (Code Creation Labs GmbH) | Free-Plan: 500 Mails je 30 Tage |
| Ein Shop-Postfach mit SMTP-Zugang, oder Google Workspace bzw. Microsoft 365 | meist vorhanden |

Preise Stand Oktober 2026. Die Mail-App wird gebraucht, weil Flow von sich aus nur interne Mails
an dein Team verschickt, keine frei gestalteten Mails an Kunden.

## Einrichtung

### 1. Apps installieren

Shopify Forms, Shopify Flow und Workflow Transactional Email (Free) aus dem App Store installieren.

### 2. Formular anlegen

Apps → Forms → Formular erstellen → **Inline**.

- Titel: `Vertrag widerrufen`
- Text: `Hiermit widerrufe ich den von mir geschlossenen Vertrag.`
- Felder. **Die Bezeichnungen genau so eintippen**, denn aus ihnen entstehen die Feldnamen, die
  der Flow später ausliest:

| Feld | Bezeichnung | Pflicht | Feldname im Flow |
|---|---|---|---|
| Vorname (Kundenfeld) | `Vor- und Nachname` | ja | `firstName` |
| E-Mail (Kundenfeld) | `E-Mail für die Eingangsbestätigung` | ja | `email` |
| Text, einzeilig | `Bestellnummer oder Vertrag (z. B. #1001)` | ja | `bestellnummerOderVertragZB1001` |
| Text, mehrzeilig | `Betroffene Artikel (optional, leer lassen für die ganze Bestellung)` | nein | `betroffeneArtikelOptionalLeerLassenFrDieGanzeBestellung` |

- Die beiden Textfelder nur im Formular speichern, nicht als Kunden-Metafeld.
- Schaltfläche: `Widerruf bestätigen`
- Marketing: „Kund:innen nicht für Marketing anmelden“. „Mit Shop anmelden“ aus.
- Hinweistext unter dem Formular: `Ihre Angaben nutzen wir nur zur Bearbeitung des Widerrufs.`
  mit Link zur Datenschutzerklärung.
- Erfolgsmeldung: `Widerruf eingegangen` und `Die Eingangsbestätigung mit Inhalt, Datum und
  Uhrzeit ist per E-Mail unterwegs.`
- Eine Benachrichtigung aus Forms brauchst du nicht; die Kopie kommt in Schritt 7 per BCC.
- Formular **aktivieren**.

Sonst nichts auf die Seite: keine Umtauschangebote, Gutscheine oder Zwischenschritte vor dem
Formular. Der Weg zum Widerruf soll so direkt sein wie der Button verspricht.

### 3. Formularseite

- Im Formular bei der Platzierung **„Landing Page erstellen“**. Das geht erst, wenn das Formular
  aktiv ist.
- Unter Onlineshop → Seiten prüfen, dass die Seite unter `/pages/vertrag-widerrufen` erreichbar
  ist; sonst die Adresse (Handle) der Seite anpassen.
- Onlineshop → Themes → Anpassen → App-Einbettungen: **Forms** einschalten, speichern.

### 4. Button in der Fußzeile

1. Onlineshop → Themes → **Anpassen**.
2. Links in der Gruppe **Fußzeile** auf „Abschnitt hinzufügen“ → **Benutzerdefiniertes Liquid**.
3. Den Inhalt von [vorlagen/footer-button.liquid](vorlagen/footer-button.liquid) einfügen.
4. Den Abschnitt per Ziehen über oder unter die Fußzeile setzen, **Speichern**.

Der Button übernimmt den Zweitbutton-Stil deines Themes (Dawn, Horizon und davon abgeleitete
Themes). Sieht er in deinem Theme nicht wie ein Button aus, setze Farben im `<style>`-Teil der
Vorlage. Wichtig ist, dass er sich von den normalen Fußzeilen-Links abhebt.

### 5. Menüpunkt im Kundenkonto

Inhalt → Menüs → **Hauptmenü des Kundenkontos** → Menüpunkt hinzufügen: Name
`Vertrag widerrufen`, Link: die Seite „Vertrag widerrufen“ → Speichern.

### 6. Mail-App einrichten

Apps → Workflow Transactional Email:

1. **E-Mail-Absender** → SMTP: Server, Port (meist 465), Benutzer und Passwort deines
   Shop-Postfachs → „Test Connection“ → Speichern. Alternativ Google oder Microsoft 365 verbinden.
2. **E-Mail-Layouts** → neues Layout, Typ **Rich Text / HTML**:
   - Name: `Widerruf: Eingangsbestätigung`
   - Betreff: `Eingangsbestätigung Ihres Widerrufs`
   - Vorschautext: `Ihr Widerruf ist bei uns eingegangen.`
   - Inhalt: [vorlagen/eingangsbestaetigung.html](vorlagen/eingangsbestaetigung.html). Vorher oben
     Shopname, Rücksendeadresse und Pflichtangaben wie im Impressum eintragen.
   - Die Vorschau zeigt Code statt Werte. Ob alles stimmt, zeigt erst die Testmail in Schritt 8.
3. In den Einstellungen der App Fehlermeldungen an dein Shop-Postfach schicken lassen.

### 7. Ablauf in Shopify Flow

Apps → Flow → Workflow erstellen:

1. Trigger **Metaobject entry created** (Metaobjekteintrag erstellt), Definition: dein Formular
   „Vertrag widerrufen“.
2. Aktion **Send Transactional Email** hinzufügen. **Zuerst** „Konfiguration öffnen“: Absender und
   Layout „Widerruf: Eingangsbestätigung“ wählen, speichern, zurück zum Flow.
3. **Danach** die Felder ausfüllen (die Konfigurationsseite leert Reply-To und Absendername):

| Feld | Inhalt |
|---|---|
| To | `{{ metaobject.email }}` |
| BCC | dein Shop-Postfach |
| Reply-To | dein Shop-Postfach |
| From name | dein Shopname |
| Language | `de` |
| Variables (JSON) | Inhalt von [vorlagen/flow-variablen.json](vorlagen/flow-variablen.json) |

4. Den Workflow oben in `Widerruf: Eingangsbestätigung` umbenennen und **aktivieren**.

### 8. Testen

- Im privaten Browserfenster: Button auf Start-, Produkt-, Warenkorb- und Rechtstextseite, am
  Handy und am Rechner. Klick führt direkt zum Formular.
- Formular mit deiner eigenen Adresse und der Bestellnummer `TEST-1` abschicken.
- Innerhalb einer Minute sollte Folgendes da sein:
  - die Mail mit deinen Angaben, dem Datum und der **deutschen Uhrzeit**
  - die Kopie im Shop-Postfach
  - der Eintrag unter Forms → Einsendungen
  - im Verlauf der Mail-App der Status „Erfolgreich“
- Forms legt für jede Einsendung ein Kundenprofil an. Das Testprofil unter Kunden wieder löschen.

### 9. Rechtstexte ergänzen

Widerrufsbelehrung und Datenschutzerklärung nach
[vorlagen/rechtstexte.md](vorlagen/rechtstexte.md) ergänzen (Einstellungen → Richtlinien).

## Betrieb

- **Mail kam nicht an:** Mail-App → Verlauf (Fehler, leere Werte), dann Flow → Ausführungen. Die
  Bestätigung in diesem Fall von Hand nachschicken.
- **Mehr als 500 Widerrufe in 30 Tagen:** Dann braucht die Mail-App einen bezahlten Plan.
- **Theme gewechselt:** Schritt 4 im neuen Theme wiederholen, sonst fehlt der Button.
- **Aufbewahrung:** Alte Einsendungen unter Forms → Einsendungen löschen, sobald keine
  Aufbewahrungspflicht mehr besteht.

## Stolperfallen

- Die Feldnamen entstehen aus den Bezeichnungen, Umlaute fallen dabei weg („Für“ wird zu `Fr`).
  Heißen deine Felder anders, findest du die Namen im Flow über „Variable hinzufügen“ →
  `metaobject`, und passt `flow-variablen.json` an.
- Die Code-Felder im Flow schließen `{{` selbst. Beim Tippen nur `{{ metaobject.email` eingeben.
- Flow liefert Zeiten in UTC. Die Mail-Vorlage rechnet sie auf deutsche Zeit um.
- Änderungen an einem aktiven Workflow: erst „Neueste Version laden“, dann „Änderungen anwenden“.

## Bewusst nicht enthalten

- **Eigene App oder eigener Server:** Die Bordmittel reichen, es gibt nichts zu warten.
- **Automatische Verknüpfung mit der Bestellung:** Bei wenigen Widerrufen im Monat sucht der
  Kundenservice die Nummer schneller selbst.
- **Button an jeder einzelnen Bestellung im Kundenkonto:** Das ginge nur mit einer eigenen App.
  Der Menüpunkt im Kundenkonto und der Button auf jeder Seite genügen.

## Lizenz

[CC0 1.0](LICENSE): frei verwendbar, auch ohne Namensnennung. Ohne Gewähr.
