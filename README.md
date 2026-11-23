# Home Assistant Blueprints: Nähe-Alarm & Heiß-und-Kalt-Spiel

*Das alte Kinderspiel in digital! Oder wie man seine Frau auch auf jedem Weihnachtsmarkt findet …*

Zwei GPS-basierte Blueprints für Home Assistant:

- **Nähe-Alarm** – meldet, wenn sich zwei Personen oder Tracker näher kommen als X Meter (aber nicht, wenn einer zuhause ist).
- **Heiß & Kalt** – das Kinderspiel „Heiß und kalt“ per GPS, mit Schatz-Modus und Dashboard-Karte.

> 🇬🇧 *Two GPS-based Home Assistant blueprints: a proximity alert between two people/trackers, and a "hot and cold" treasure hunt game for kids. UI texts are in German.*

## Voraussetzungen

- Home Assistant **2024.10 oder neuer** (neue Trigger-/Action-Syntax, Input-Sections)
- [Companion App](https://companion.home-assistant.io/) (iOS oder Android) mit aktiviertem Standort
- Metrisches Einheitensystem (`distance()` liefert dann Kilometer)
- Optional: [FaserF/ha-whatsapp](https://github.com/FaserF/ha-whatsapp) für WhatsApp-Versand. Ohne die Integration einfach „WhatsApp senden“ ausgeschaltet lassen.

---

## 📍 Nähe-Alarm

[![Blueprint importieren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FPWolters%2Fha-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fnaehe_alarm.yaml)

Benachrichtigt, sobald der Abstand zwischen zwei Entitäten **unter** eine Schwelle fällt.

- Keine Meldung, wenn eine der beiden Entitäten `home` ist – auch nicht, wenn beide gemeinsam das Haus verlassen.
- Eine Sperrzeit (Standard 30 min) verhindert Mehrfachmeldungen durch GPS-Springen.
- Versand per Push und/oder WhatsApp an mehrere Empfänger, beide Kanäle unabhängig voneinander.

Keine Helfer nötig – importieren, Automation anlegen, fertig.

---

## 🎯 Heiß & Kalt – Suchspiel

[![Blueprint importieren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FPWolters%2Fha-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fheiss_kalt.yaml)

Ein Sucher bekommt aufs Handy „Wärmer!“ oder „Kälter!“ mit einer Temperaturstufe von 🧊 Eiskalt bis 🔥🔥 Glühend heiß – bis er das Ziel gefunden hat.

**Ziele:**
- **Versteckter Schatz** – jemand geht zum Versteck und drückt „Schatz hier verstecken“. HA speichert die GPS-Position der Person, die gedrückt hat.
- **Person, Tracker oder Zone** – z. B. Mama oder Papa verstecken sich selbst.

### Installation

1. **Blueprint importieren** (Button oben).
2. **Package einrichten:** `packages/heiss_kalt.yaml` nach `/config/packages/` kopieren. Falls noch nicht vorhanden, in `configuration.yaml` ergänzen:
   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```
   Das Package enthält die Helfer (Spielschalter, Entfernungsspeicher, Schatz-Koordinaten, Verstecken-Knopf) und die Automation „Schatz verstecken“.
3. **HA neu starten** (für den neuen `input_button` reicht ein YAML-Reload meist nicht).
4. **Automation aus dem Blueprint anlegen:** Sucher und Notify-Dienst seines Handys eintragen. Die Helfer sind schon vorbelegt.
5. **Optional: Dashboard-Karte** aus `dashboard/heiss_kalt_karte.yaml` über *Karte hinzufügen → Manuell* einfügen und `automation.heiss_kalt_suchspiel` (2×) durch die Entity-ID deiner Automation ersetzen. Nur Core-Karten, kein HACS.

> **Ohne Packages?** Die Helfer lassen sich auch unter *Einstellungen → Geräte & Dienste → Helfer* anlegen (1× Schalter, 3× Zahl im Eingabefeld-Modus, 1× Taste) und im Blueprint auswählen. Die Verstecken-Automation muss dann aus dem Package-YAML übernommen werden.

### Spielablauf

1. Verstecker geht zum Versteck und drückt **Schatz verstecken**. Er bekommt eine Bestätigung mit GPS-Genauigkeit (Warnung bei schlechter als ±30 m).
2. **Spiel starten** – per Karte, Schalter „Spiel läuft“ oder „Ausführen“ der Automation. Läuft schon eine Runde, wird sie neu gestartet.
3. Ab einer Änderung von 25 m kommt ein neuer Hinweis, unter 20 m heißt es **🎉 Gefunden!** und das Spiel endet.

Alle Werte (Spielfeldgröße, Mindeständerung, Fund-Radius) sind im Blueprint einstellbar. Faustregel für die Spielfeldgröße: ungefähr die Startentfernung.

### Gut zu wissen

- **GPS-Genauigkeit:** Für den eigenen Garten zu ungenau, ab etwa Straßenzug-Größe macht es Spaß.
- **iOS drosselt Standort-Updates im Hintergrund.** Der Blueprint fordert alle 30 s ein Update an, am zuverlässigsten läuft es aber mit der Companion App im Vordergrund.
- **Verstecker** braucht einen HA-Benutzer, der einer Person mit Companion App zugeordnet ist. Der Notify-Dienst wird aus dem Tracker abgeleitet (`device_tracker.xyz` → `notify.mobile_app_xyz`). Hast du den Dienst umbenannt, kommt keine Bestätigung – der Schatz wird trotzdem gespeichert.
- Die Karte zeigt bewusst **keine Entfernung und keine Landkarte**, damit niemand spickt.

---

## Lizenz

[MIT](LICENSE)
