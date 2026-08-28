# Camper Display

Ein ESPHome-basiertes LVGL-Dashboard für ein Waveshare 7" Touch-Display (ESP32-S3), das zentrale Fahrzeugdaten aus Home Assistant anzeigt und steuerbar macht – gedacht für den Einbau in Wohnmobil/Camper.

![Platform](https://img.shields.io/badge/platform-ESP32--S3-blue)
![ESPHome](https://img.shields.io/badge/ESPHome-2025.8.0%2B-brightgreen)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

## Funktionen

**Startseite**
- Frischwasser- und Grauwasser-Füllstand sowie Batterieladung als Rundinstrumente mit Farbverlauf (Grün/Rot je nach Füllstand-Logik)
- Navigation zu allen weiteren Seiten

**Truma-Heizung**
- Großes Thermostat-Widget im Home-Assistant-Klimakarten-Stil (Drehrad zum Einstellen der Zieltemperatur)
- Separate Boiler-Steuerung mit festen Temperaturstufen (Aus/40°/60°/80°)
- Fehler- und Verbindungsstatus

**Position**
- Wasserwaage/Neigungsanzeige, nachgebaut nach dem Design der `realistic-bubble-level-card`
- Reifendruck, -temperatur und Batteriestatus aller vier Reifen (TPMS), an den Fahrzeugecken angeordnet
- Nivellierungs-Empfehlung: berechnet aus Neigungswinkel, Spurbreite und Radstand, wie viele Zentimeter unter welches Rad gelegt werden sollten

**Heizung Hinten**
- Zweite Heizzone (Home-Assistant `climate`-Entity) mit eigenem Thermostat

**Licht**
- Ein/Aus- und Helligkeitssteuerung für vier Lampen

**Allgemein**
- 5-Minuten-Inaktivitäts-Timeout für das Backlight, Touch-Wakeup
- Alle Home-Assistant-Entity-IDs sowie Tankgrößen und Fahrzeugmaße zentral über `substitutions:` konfigurierbar – kein Durchsuchen der Datei nötig

## Hardware

- Waveshare ESP32-S3-Touch-LCD-7 (800×480, RGB-Panel, GT911-Touch, CH422G-IO-Expander)
- Home Assistant mit ESPHome-Add-on
- Optional: TPMS-Reifendrucksensoren, die in Home Assistant als Entities vorliegen

## Voraussetzungen

- Home Assistant mit installiertem [ESPHome Add-on](https://esphome.io/)
- Die auf dem Display angezeigten Home-Assistant-Entities existieren bereits in deiner Installation (siehe [Einrichtung](#einrichtung))

## Einrichtung

1. `camper-display.yaml` in dein ESPHome-Verzeichnis kopieren
2. `secrets.yaml` um deine WLAN-Zugangsdaten ergänzen:
   ```yaml
   wifi_ssid: "Dein-WLAN"
   wifi_password: "Dein-Passwort"
   ```
3. Im Kopf der Datei den Abschnitt **`substitutions:`** an deine eigenen Home-Assistant-Entity-IDs anpassen – das ist der einzige Teil, den du normalerweise ändern musst. Enthält u. a.:
   - Tank- und Batterie-Sensoren
   - Truma-Heizung (Schalter, Sensoren, Zieltemperaturen)
   - Neigungssensoren für die Wasserwaage
   - TPMS-Reifensensoren (Druck, Temperatur, Batterie – je Rad)
   - Zweite Heizzone (`climate`-Entity)
   - Lichter (vier Lampen)
   - Tankgrößen (Liter) und Fahrzeugmaße (Spurbreite/Radstand, für die Nivellierungs-Empfehlung)
4. In ESPHome kompilieren und auf das Gerät flashen

## Bekannte Einschränkungen

- **Kein ESPHome-Designer-Roundtrip**: Diese Datei enthält viel handgeschriebene LVGL-Logik (`on_click`, `on_value`, dynamische `!lambda`-Bindungen). Ein Export/Re-Import über den visuellen [ESPHome Designer](https://github.com/koosoli/ESPHomeDesigner) verwirft diese Anpassungen zuverlässig. Änderungen bitte direkt in der YAML vornehmen.
- **Speicherbedarf beim Kompilieren**: Bei limitiertem RAM (z. B. Home Assistant auf einem Raspberry Pi) kann der Compile-Vorgang mit `Killed signal terminated program cc1plus` abbrechen. Abhilfe: im ESPHome-Add-on `compile_process_limit: 1` setzen, oder Swap-Speicher hinzufügen.
- **Vorzeichen-Richtung der Nivellierungs-Empfehlung** ist eine Annahme und muss am eigenen Fahrzeug verifiziert werden (siehe Kommentare im Script `update_level_empfehlung`).
- Getestet mit ESPHome ab Version 2025.8.0.

## Tank-/Fahrzeugmaße anpassen

Direkt im `substitutions:`-Block, kein Suchen im Layout-Code nötig:

```yaml
tank_frischwasser_liter: "200"
tank_grauwasser_liter: "100"
fahrzeug_spurbreite_cm: "200"
fahrzeug_radstand_cm: "400"
```

| Wert | Substitution | Standardwert |
|---|---|---|
| Frischwasser-Skala | `tank_frischwasser_liter` | 200 Liter |
| Grauwasser-Skala | `tank_grauwasser_liter` | 100 Liter |
| Batterie-Skala | – (fest 0–100 %) | 100 % |
| Spurbreite | `fahrzeug_spurbreite_cm` | 200 cm |
| Radstand | `fahrzeug_radstand_cm` | 400 cm |

## Mitwirken

Issues und Pull Requests sind willkommen. Bei strukturellen Änderungen bitte kurz beschreiben, welche Home-Assistant-Domain (z. B. `switch`, `climate`, `light`) betroffen ist, da sich die Steuerlogik je nach Domain unterscheidet.

## Lizenz

MIT – siehe [LICENSE](LICENSE)
