# V-Modul 526 – Versuch 3: Luciferase-Reportergen-Assay

Auswertung des Luciferase-Reportergen-Assays zur Messung der G<sub>s</sub>-Signale des
MC4 Rezeptors (Praktikum V526, Institut für Zellbiologie, HHU Düsseldorf).

## Inhalt

| Datei | Beschreibung |
|---|---|
| `Auswertung_Luciferase_Reportergen_Assay.ipynb` | Das Auswertungs-Notebook (JupyterLite-tauglich) |
| `daten/1_3_6_7_ONE_Glo_Lumineszenz_Modified_20260910_145312_1.xlsx` | Rohdaten-Export des Tecan Spark Cyto |

## Nutzung in JupyterLite

1. JupyterLite öffnen (z. B. <https://jupyter.org/try-jupyter/lab/>).
2. Das Notebook **und** die Excel-Datei per Drag & Drop in den Dateibrowser links ziehen.
   Die Excel-Datei kann direkt neben dem Notebook oder in einem Unterordner `daten/`
   liegen – beides wird gefunden.

   Gebraucht wird der **Lumineszenz-Export des Reportergen-Assays** (384-Well,
   16 Zeilen × 24 Spalten). Liegen mehrere Excel-Dateien im Dateibrowser, wählt das
   Notebook anhand des *Inhalts* die passende aus und listet auf, was es geprüft hat –
   der 96-Well-Export des Oberflächen-ELISA aus Versuch 2 wird dabei erkannt und
   übersprungen.
3. Notebook öffnen und *Run → Run All Cells* ausführen.

Es wird **nichts nachinstalliert**: `numpy`, `pandas`, `scipy` und `matplotlib` bringt
JupyterLite bereits mit, und die Excel-Datei wird allein mit der Standardbibliothek
gelesen (eine `.xlsx`-Datei ist ein ZIP-Archiv mit XML-Dateien). `openpyxl` wird nicht
benötigt. Dadurch gibt es keine Abhängigkeit, die beim Start fehlschlagen kann, und
dasselbe Notebook läuft unverändert auch in einem lokalen Jupyter.

## Aufbau des Notebooks

1. **Module importieren** – alle Imports gebündelt in einer Zelle, ohne
   Nachinstallation und ohne `await`
2. **Versuchsaufbau und Pipettierschema** – Konfiguration des Plattenblocks
3. **Rohdaten einlesen** – Plattenraster aus dem Tecan-Export; erkennt automatisch
   normales Raster, transponiertes Raster und Listenformat (Wellnamen wie `A1`),
   durchsucht alle Tabellenblätter und zeigt bei Problemen den Aufbau der Datei
4. **Wells den Konditionen zuordnen** – nach Pipettierschema (Skript S. 28, Abb. 7/8)
5. **Mittelwerte und Streuung** der Triplikate, inkl. Prüfung auf auffällige Replikate
6. **Normierung** auf den unstimulierten Leervektor (pcDps, 0 M)
7. **Abbildung 1** – Konzentrations-Wirkungs-Kurve
8. **Abbildung 2** – Kontrollen (Forskolin vs. DMEM)
9. **Kurvenanpassung und EC<sub>50</sub>** – *log(agonist) vs. response*, 3 Parameter
10. **Abbildung 3** – Gesamtdarstellung im Stil der Auswertungsvorlage
11. **Ergebnisse sichern** – CSV-Export und PNG-Abbildungen (300 dpi)
12. **Qualitätskontrolle** – automatische Bewertung, ob der Assay funktioniert hat
13. **Zusammenfassung**

## Plattenbelegung (Block dieser Gruppe)

Messwerte: **Spalten 13–18, Zeilen A–P** der 384-Well-Platte.

* Spalten **13–15**: MC4R (Zeilen A, C, E, G, I, K, M, O) und
  mut. MC4R (Zeilen B, D, F, H, J, L, N, P)
* Spalten **16–18**: pcDps (nur Zeilen A, C, E, G, I, K, M, O)

| Zeilen | Stimulation |
|---|---|
| A / B | 10 µM Forskolin (Positivkontrolle) |
| C / D | 0 M α-MSH (DMEM, unstimuliert) |
| E / F | 10⁻¹¹ M α-MSH |
| G / H | 10⁻¹⁰ M α-MSH |
| I / J | 10⁻⁹ M α-MSH |
| K / L | 10⁻⁸ M α-MSH |
| M / N | 10⁻⁷ M α-MSH |
| O / P | 10⁻⁶ M α-MSH |

## Rechenweg

1. Mittelwert der drei technischen Replikate je Konstrukt und Bedingung
2. Normierung auf den unstimulierten Leervektor:

   `x-fold = Mittelwert(Konstrukt, Bedingung) / Mittelwert(pcDps, 0 M)`

3. Kurvenanpassung an DMEM (aufgetragen bei log[M] = −13) sowie 10⁻¹¹ … 10⁻⁶ M:

   `Y = Bottom + (Top − Bottom) / (1 + 10^(LogEC50 − X))`

Fehlerbalken werden laut Praktikumsskript nicht dargestellt, da der Versuch nur einmal
durchgeführt wurde. Die Standardabweichung der Triplikate wird trotzdem berechnet und
dient der Qualitätsbeurteilung.

## Anpassung an einen anderen Plattenblock

Alle Einstellungen stehen gebündelt in der Konfigurationszelle in Abschnitt 2
(`SPALTEN_REZEPTOR`, `SPALTEN_PCDPS`, `ZEILEN_SCHEMA`, `DATEINAME`). Für eine andere
Gruppe genügt es, die Spaltennummern dort zu ändern.
