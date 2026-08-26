# Camper Display

Ein ESPHome-basiertes LVGL-Dashboard für ein Waveshare 7" Touch-Display (ESP32-S3), das zentrale Fahrzeugdaten aus Home Assistant anzeigt und steuerbar macht – gedacht für den Einbau in Wohnmobil/Camper.

![Platform](https://img.shields.io/badge/platform-ESP32--S3-blue)
![ESPHome](https://img.shields.io/badge/ESPHome-2025.8.0%2B-brightgreen)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

## Funktionen

- **Startseite**: Frischwasser- und Grauwasser-Füllstand sowie Batterieladung als Rundinstrumente mit Farbverlauf (Grün/Rot je nach Füllstand-Logik)
- **Truma-Heizung**: großes Thermostat-Widget im Home-Assistant-Klimakarten-Stil (Drehrad zum Einstellen der Zieltemperatur), separate Boiler-Steuerung mit festen Temperaturstufen
- **Position/Wasserwaage**: Neigungsanzeige für Camper-Nivellierung, nachgebaut nach dem Design der `realistic-bubble-level-card`
- **Heizung Hinten**: zweite Heizzone (Home-Assistant `climate`-Entity) mit eigenem Thermostat
- **Licht**: Ein/Aus- und Helligkeitssteuerung für vier Lampen
- 5-Minuten-Inaktivitäts-Timeout für das Backlight, Touch-Wakeup
- Alle Home-Assistant-Entity-IDs zentral über `substitutions:` konfigurierbar – kein Durchsuchen der Datei nötig

## Hardware

- Waveshare ESP32-S3-Touch-LCD-7 (800×480, RGB-Panel, GT911-Touch, CH422G-IO-Expander)
- Home Assistant mit ESPHome-Add-on

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
3. Im Kopf der Datei den Abschnitt **`substitutions:`** an deine eigenen Home-Assistant-Entity-IDs anpassen – das ist der einzige Teil, den du normalerweise ändern musst:
   ```yaml
   substitutions:
     entity_frischwasser_stand: "sensor.dein_frischwassersensor"
     entity_grauwasser_stand: "sensor.dein_grauwassersensor"
     entity_batterie_stand: "sensor.deine_batterie"
     entity_truma_raumheizung_schalter: "switch.deine_heizung"
     # ... usw., siehe Kommentare in der Datei
   ```
4. In ESPHome kompilieren und auf das Gerät flashen

## Bekannte Einschränkungen

- **Kein ESPHome-Designer-Roundtrip**: Diese Datei enthält viel handgeschriebene LVGL-Logik (`on_click`, `on_value`, dynamische `!lambda`-Bindungen). Ein Export/Re-Import über den visuellen [ESPHome Designer](https://github.com/koosoli/ESPHomeDesigner) verwirft diese Anpassungen zuverlässig. Änderungen bitte direkt in der YAML vornehmen.
- **Speicherbedarf beim Kompilieren**: Bei limitiertem RAM (z. B. Home Assistant auf einem Raspberry Pi) kann der Compile-Vorgang mit `Killed signal terminated program cc1plus` abbrechen. Abhilfe: im ESPHome-Add-on `compile_process_limit: 1` setzen, oder Swap-Speicher hinzufügen.
- Getestet mit ESPHome ab Version 2025.8.0.

## Tank-/Batterie-Skalen anpassen

Die Wertebereiche der Rundinstrumente lassen sich direkt im `substitutions:`-Block anpassen – kein Suchen im Layout-Code nötig:

```yaml
tank_frischwasser_liter: "200"
tank_grauwasser_liter: "100"
```

| Anzeige | Substitution | Standardwert |
|---|---|---|
| Frischwasser | `tank_frischwasser_liter` | 200 Liter |
| Grauwasser | `tank_grauwasser_liter` | 100 Liter |
| Batterie | – (fest 0–100 %) | 100 % |

## Mitwirken

Issues und Pull Requests sind willkommen. Bei strukturellen Änderungen bitte kurz beschreiben, welche Home-Assistant-Domain (z. B. `switch`, `climate`, `light`) betroffen ist, da sich die Steuerlogik je nach Domain unterscheidet.

## Lizenz

MIT – siehe [LICENSE](LICENSE)
