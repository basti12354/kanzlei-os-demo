# Lokaler OCR-Prüfkorpus

Alle Daten sind synthetisch. Die Referenzen sind vorhandene Text-PDFs; ihre Bytes bleiben unverändert. Die Manifeste verbinden Original und Rastervarianten über SHA256. Die sauberen Bild-PDFs enthalten sichtbaren Text ausschließlich als RGB-Pixel. In der schweren Variante ist der Betrag der Lohnsteuer bzw. der Rechnung gezielt überdeckt. PNGs zeigen die Raster vor dem Einbetten.

Die gespeicherten Antworten sind **simuliert**: aus der Textreferenz abgeleitete, eingefrorene Testausgaben, keine Ergebnisse eines OCR-Werkzeugs. Das Skript liest niemals `sollwerte.json`. Die separat hinterlegten, fachlich festgelegten Sollwerte enthalten für jedes ausgewiesene Fachfeld Wert, Seite, Fundstelle und Prüferwartung. Schwere Varianten überschreiben nur den betroffenen Feldsollwert mit null; die intakten Felder bleiben gleich. Ein völlig fehlendes Feld wird separat mit null/keiner Seite geprüft.

Vertrag: JSON-Hülle Version 1 mit Dokument-ID, Variante, Originaldatei samt Prüfsumme, Herkunft und `seiten: PdfSeite[]`. Jede Seite bleibt auch ohne Text enthalten, Nummern beginnen bei 1. Jede Zeile enthält geordnete `zellen` und deren mit Leerzeichen verbundenen `text`. Später liefert ein echter OCR-Adapter dieselben Seiten an den gemeinsamen Feldrunner in `tests/ocr-korpus.test.ts`; die bestehenden Sollwerte bleiben unverändert. Eine echte Adapterantwort bekommt ihre tatsächliche Herkunft statt des Simulationsetiketts; der Fixture-Lader akzeptiert absichtlich nur die hier gespeicherten Simulationen.

FiBu projiziert Seiten auf Textzeilen und hält Seiten/Zeilenindex parallel in `fibuZeilen` fest. `belegAusText(..., { fehlendeBetraegeAbleiten: false })` verhindert im Scanvertrag das Ergänzen fehlender Beträge. Die bestehende Rechnungsauslese darf auf ausdrücklichem Standardpfad weiterhin fehlende Summen berechnen. Widersprüchliche, beschädigte oder mehrdeutige ausdrücklich vorhandene Beträge sperren diese Berechnung auch auf dem Standardpfad. Quellenreferenzen im Korpus sind Prüfbelege; die bestehende Anwendung speichert noch keine OCR-Seitenmetadaten.

Reproduktion ohne Download/Server/Browser: `node scripts/ocr-testdaten.mjs`. Verwendet nur lokale pdfjs-dist- und dessen Canvas-Pakete, Systemfonts und Node-zlib. Die vollständigen Binärdateien sind eingecheckt; identische Umgebung liefert identische Prüfsummen. Neue Plattform-/Fontversionen können eine neue Rasterkodierung ergeben und erfordern einen bewussten Fixture-Review. Das Skript verändert ausschließlich die drei zugeordneten neuen Korpusfälle und ihre Manifeste/Antworten, niemals die Referenzen oder Sollwerte.

## Fallmatrix und Ausführung

Soll/Ist und Status stehen im Abschlussprotokoll `PRUEFPROTOKOLL.md`. Alle hier aufgeführten Prüffälle laufen mit `npm run test -- tests/ocr-korpus.test.ts`; mutierte Antworten in den Gegenfällen sind ebenfalls bewusst simuliert.

| Fall | Eingabe / Schritte | Soll |
| --- | --- | --- |
| OCR-01 | Drei Manifeste, Textreferenz und je zwei Bild-PDFs | Inventar und Originalbezug vollständig |
| OCR-02 | Alle 11 ESt- bzw. 12 FiBu-Felder je Variante | Eigenes Wert-/Seiten-/Fundstellenorakel vollständig |
| OCR-03 | Hülle, Seiten, Zellen prüfen; defekte Antworten zurückweisen | Version/Struktur konsistent |
| OCR-04 | ESt Standard über Text und saubere Scanantwort | 4860000/613242 Cent, Seite 1 |
| OCR-05 | Mehrseitiger Fall und leere Startseite | 3165040/461723 Cent auf Seite 2; keine Verschiebung |
| OCR-06 | Rechnung über Text und saubere Scanantwort | 6000/1140/7140 Cent; Partner, Datum und Art gleich |
| OCR-07 | NBSP, Leerraum, sortierte Zellen, Tausender, Minus, Null | Werte korrekt; explizite 0 bleibt 0 |
| OCR-08 | Nummer/Bezeichnung/Nachbarzeile/Jahr/Dokumentart manipulieren | Keine fremde sichere Übernahme |
| OCR-09 | Überdeckte und ganz fehlende Lohnsteuerzeile | null, manuell; andere Felder intakt |
| OCR-10 | Abweichende Wiederholung gleicher/verschiedener Seiten | null, Konfliktquellen erhalten; identische Wiederholung erlaubt |
| OCR-11 | Zwei Beträge, O/0, defekte Trennzeichen/Dezimalstellen | Keine erratene Zahl oder Regex-Teilübernahme |
| OCR-12 | Sechs Bild-PDFs und alter Grafikscan durch Textleser | Leere Zeilen, keine OCR behauptet |
| OCR-13 | FiBu-Konflikt/unlesbare/fehlende Summe trotz anderer Beträge | Keine Rekonstruktion unsicherer Summen; Option trennt Ableitung |
| OCR-14 | Simulierte Scanantwort über PdfLeser-Port in Anwendung | Freigabe gesperrt; erst ausdrückliche manuelle Erfassung klärt |
| OCR-15 | Gemeinsamer Feldrunner für beide Lieferanten | Unveränderte Sollwerte, keine Anbieter-Fachlogik |
| OCR-16 | Herkunft aller Antworten prüfen | Als simuliert markiert; nur lokaler Schnittstellenvertrag |
| OCR-17 | Grenzen lesen | Anbieterzugänge, Plattformadapter, OCR-Qualität und Produktivabnahme offen |
| OCR-18 | Korpus, Regression, check/build/diff prüfen | Nachweise und exakter Commit, unabhängige Prüfung anschließend |

Die produktive Abnahme bleibt offen: Wahl und Einrichtung des echten OCR-Adapters, Anbieterzugänge, Plattformadapter, repräsentative OCR-Qualität, Datenschutz-/Betriebsentscheidung und fachliche Produktivabnahme. Diese Simulation beweist ausschließlich den lokalen Schnittstellenvertrag und die anschließende Fachlogik. Kein produktiver OCR-Pfad oder Anbieterzugang wurde eingerichtet.
