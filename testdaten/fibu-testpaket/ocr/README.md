# FiBu-Anteil des OCR-Prüfkorpus

Vertrag, Fallmatrix und Grenzen: [gemeinsame Dokumentation](../../est-testpaket/ocr/README.md).

`fibu-rechnung.manifest.json` verweist auf die unveränderte synthetische Textrechnung und die saubere bzw. schwere Raster-PDF. Die schwere Variante überdeckt die Rechnungssumme. Die Antwortdateien sind ausdrücklich **simuliert**, keine OCR-Ergebnisse. Die unabhängige Datei `sollwerte.json` enthält alle zwölf ausgegebenen Fachfelder mit Sollwert, Seite, Fundstelle und Prüferwartung. Für die schwere Variante bleibt brutto null, während Netto und USt erkannt werden. Keine Rekonstruktion im Scanvertrag.

Die Quellenprojektion ist getrennt vom Fachwert prüfbar. Anbieterzugänge, Plattformadapter, OCR-Qualität und Produktivabnahme bleiben offen. Der Anwendungstest injiziert die gespeicherte schwere Antwort bewusst hinter dem vorhandenen Lese-Port, überprüft die Sperre und die explizite manuelle Klärung; das ist kein installierter OCR-Adapter.
