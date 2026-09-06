# Camper Display

Touchscreen-Steuerzentrale fürs Wohnmobil – Heizung, Klimaanlage, Licht und
Fahrzeug-Sensoren auf einem Waveshare 7"-Touchdisplay (ESP32-S3), gebaut mit
[ESPHome](https://esphome.io) und [Home Assistant](https://www.home-assistant.io).

Teil des **SmartWomoHA**-Projekts – *"Help for free"*.

## Was das Display kann

### 🌡️ Heizung & Klima
- Truma Combi 6 Heizung: Ein/Aus, Solltemperatur per Drehregler, Heizstufe (ECO/BOOST)
- Truma Aventa Klimaanlage: Aus/Kühlen/Nur Lüften, Lüfterstufe (Low/Mid/High)
- Boiler: Aus/40°/60°/80°C auf Knopfdruck
- Fehleranzeige mit Fehlercode direkt auf dem Display
- Separate 230V-Zusatzheizung hinten mit eigenem Thermostat

### 💡 Licht (8 Lampen steuerbar)
- An/Aus + Helligkeit für jede Lampe
- Bei RGB-fähigen Lampen: eigene Farbregler (Rot/Grün/Blau) + Warmweiß
- Lichteffekte (z. B. "Random", "Langsam", "Schnell") direkt wählbar
- Erkennt automatisch, welche Lampe überhaupt RGB kann, und blendet den Regler
  sonst aus (basierend auf Home Assistants `supported_color_modes`-Attribut)

### 📊 Übersicht & Sensoren
- Frischwasser-, Grauwasser- und Batteriestand als Rundinstrumente
- Wasserwaage (Ausrichtung des Fahrzeugs) mit Blasenanzeige
- An allen vier Ecken: Reifendruck, -temperatur und Achshöhe (zum Nivellieren)

## Hardware

- Waveshare ESP32-S3-Touch-LCD-7 (800×480, GT911-Touch, LVGL)
- 16 MB Flash
- Truma-Heizung/Klima angebunden über CI-Bus (LIN) via
  [`havanti/esphome-truma`](https://github.com/havanti/esphome-truma)
  (Fork von `Fabian-Schmidt/esphome-truma_inetbox`)

## Setup

1. Repo klonen
2. `secrets.yaml.example` zu `secrets.yaml` kopieren und mit echten
   Zugangsdaten befüllen (WLAN, API-Verschlüsselung, OTA-Passwort, ggf. MQTT)
3. Eigene Bilddateien in `images/` legen (siehe `images/README.md`)
4. Die `substitutions:` am Anfang von `camper-display.yaml` an deine eigenen
   Home-Assistant-Entity-IDs anpassen (Lichter, Tanks, Reifendruck-Sensoren, etc.)
5. Erstes Flashen per USB-Kabel:
   ```
   esphome run camper-display.yaml
   ```
6. Alle weiteren Updates per OTA (WLAN), kein USB-Kabel mehr nötig:
   ```
   esphome upload camper-display.yaml
   ```

## Sicherheitshinweis

`secrets.yaml` **niemals** committen (steht in `.gitignore`) – dort stehen dein
WLAN-Passwort und die API-Verschlüsselung drin.

## Bekannte Einschränkungen / offene Punkte

- Die drei letzten Lichter ("Licht 6/7/8") sind als Platzhalter vorbereitet –
  Entity-IDs in `secrets.yaml`/`substitutions:` noch anzupassen, sobald die
  echten Lampen feststehen
- Bei der WiZ-Lampe wird aktuell ein fester Weiß-Kanal angenommen; falls sie
  stattdessen eine einstellbare Farbtemperatur (Kelvin) hat, müsste der
  entsprechende Regler noch angepasst werden

## Lizenz / Nutzung

Frei nutzbar und veränderbar im Sinne von "Help for free" – bei Fragen gerne
in der SmartWomoHA-Gruppe melden.
