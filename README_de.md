# 🤖 Eufy RoboVac G10 LIDAR Upgrade Project

![Status](https://img.shields.io/badge/Status-Research%20Phase-yellow)
![License](https://img.shields.io/badge/License-MIT-blue)
![Hardware](https://img.shields.io/badge/Hardware-ESP32%20%7C%20LIDAR-orange)
![Privacy](https://img.shields.io/badge/Privacy-Cloud%20Free-green)

> **Upgrading a "dumb" vacuum robot to a smart, locally-controlled cleaning machine with real SLAM capabilities**

Ein Open-Source-Projekt zur Transformation des Eufy RoboVac G10 Hybrid von einem simplen Gyroskop-basierten Zufallsreiniger zu einem intelligenten, LIDAR-gesteuerten Roboter mit echter Raumkartierung und 100% lokaler Steuerung – ohne Cloud-Abhängigkeit.

---

## 📖 Einleitung

### Motivation

Ich bin es **satt**, dass mein RoboVac wie ein Geisteskranker einfach mit der Taktik "Augen zu und durch" durch meine Wohnung rast. Der G10 Hybrid ist einfach nicht smart – er reinigt in **ungleichmäßigen Rastern**, fährt dieselben Stellen mehrfach ab, während andere komplett ignoriert werden, und verliert regelmäßig die Orientierung. 

**Mit der grundsätzlichen Leistung bin ich sonst zufrieden:** Die Saugkraft ist okay, die Wischfunktion funktioniert, und die Hardware ist solide. Das hat mich zu der Frage gebracht: **Was kann ich an diesem Ding noch verbessern?**

Bei meiner Recherche bin ich auf das hervorragende Projekt von **Rjevski** gestoßen:
- [esphome-eufy-robovac-g10-hybrid](https://github.com/Rjevski/esphome-eufy-robovac-g10-hybrid) – ESPHome-Integration zum Ersetzen des Tuya-Moduls
- [ha-eufy-robovac-g10-hybrid](https://github.com/Rjevski/ha-eufy-robovac-g10-hybrid) – Home Assistant Integration

Diese Projekte zeigen, dass der G10 **modifizierbar** ist und dass das proprietäre Tuya-Modul durch einen **ESP32 mit ESPHome** ersetzt werden kann. Das öffnet die Tür für echte Smart-Home-Integration – **ohne Cloud, ohne Anker/Eufy-Server, komplett lokal**.

### Warum smarter machen?

1. **Intelligente Navigation statt Zufall**: Echtes LIDAR-basiertes SLAM (Simultaneous Localization and Mapping) für systematische Raumabdeckung
2. **Privacy First**: Keine Verbindung zu Eufy/Anker/Tuya-Servern – 100% lokale Kontrolle über alle Daten
3. **Erweiterbarkeit**: Offene Plattform für zusätzliche Sensoren, Custom-Software und Automatisierungen
4. **Lernprojekt**: Hands-on Erfahrung mit Robotik, SLAM, ROS2, ESP32/ESPHome und IoT-Security
5. **Nachhaltigkeit**: Vorhandene Hardware upgraden statt neu kaufen

**Das Ziel:** Aus einem 150€-Budget-Roboter einen Premium-Roboter mit Funktionen wie bei Roborock/Xiaomi (300-500€) machen – für unter 100€ zusätzliche Hardware.

---

## 🎯 These / Theorie

### Vermuteter Zustand nach Umsetzung

Nach erfolgreicher Implementierung wird der Eufy RoboVac G10 Hybrid folgende Fähigkeiten besitzen:

#### ✅ **Primäre Ziele:**
- **360° LIDAR-Scan** für echte Raumerfassung (8-12m Reichweite)
- **Echtzeit-Kartierung** der Wohnung mit persistenten Maps
- **Intelligentes Path Planning** – keine zufälligen Muster mehr
- **Zonenreinigung** – "Reinige nur die Küche"
- **No-Go-Zonen** – definierte Bereiche, die gemieden werden
- **"Gehe zu Position X,Y"** – direkte Navigation zu Punkten
- **Vollständig lokale Steuerung** über Home Assistant (kein Internet nötig)
- **Hybrid-Navigation**: LIDAR als primär, Original-Sensoren (IR/Bumper) als Fallback

#### ⚙️ **Sekundäre Ziele:**
- **Live-Visualisierung** der Karte in Home Assistant/RViz
- **Verbesserter Akku-Management** – optimierte Rückkehr zur Ladestation
- **Raum-Erkennung** – "Wohnzimmer", "Küche", "Schlafzimmer"
- **Reinigungs-Statistiken** – Fläche, Zeit, Abdeckung
- **Custom-Automatisierungen** (z.B. "Reinige wenn niemand zuhause ist")

### Was wir bereits über das Modifizieren des G10 wissen

Dank der Vorarbeit von **Rjevski** und eigener Recherche:

#### **Hardware-Architektur:**
- **CPU**: Amicro ARM-basierter Microcontroller (proprietär, kein Shell-Zugriff)
- **WiFi-Modul**: Tuya-kompatibles Modul mit **4 verdrehten Kabeln**:
  - Rot: 3.3V
  - Grün: GND
  - Blau: RX (zu Mainboard TX)
  - Schwarz: TX (zu Mainboard RX)
- **UART-Kommunikation**: 115200 Baud, 8N1
- **Tuya-Protokoll**: Dokumentiertes Protokoll für Steuerung
- **Sensoren**: Gyroskop, IR-Bumper, Cliff-Sensoren (4x Absturzerkennung)

#### **Bewiesene Modifikationen:**
1. ✅ **Tuya-Modul-Austausch**: ESP8266/ESP32 kann als Drop-in-Replacement fungieren
2. ✅ **ESPHome-Integration**: Stabile UART-Kommunikation mit Original-Firmware
3. ✅ **Home Assistant Control**: Volle Steuerung über lokales Netzwerk
4. ✅ **Datapoint-Mapping**: Tuya DPs bekannt (Power, Mode, Fan Speed, Battery, etc.)
5. ✅ **OTA-Updates**: Over-the-Air-Firmware-Updates via ESPHome möglich

#### **Bekannte Einschränkungen:**
- ⚠️ **Kein Root-Zugriff** auf Amicro-CPU (kein bekannter Exploit)
- ⚠️ **Original-Firmware erforderlich**: ESP32 agiert als Proxy, nicht als Ersatz
- ⚠️ **Begrenzte GPIO-Pins** am ESP32 (UART für G10, zusätzliche für LIDAR/Sensoren)

### Was ist in der Theorie realistisch umsetzbar?

#### **Hohe Wahrscheinlichkeit (>80%):**

1. **LIDAR-Integration via ESP32**
   - **Hardware**: Roborock/Xiaomi LDS-Modul (40-90€) oder YDLIDAR X2L (~60€)
   - **Anschluss**: ESP32 → 2. UART für LIDAR-Daten
   - **Protokoll**: Gut dokumentiert (RPLIDAR/SLAMTEC-kompatibel)
   - **Streaming**: TCP/IP-Stream zu Raspberry Pi für SLAM-Processing

2. **ROS2 SLAM auf Raspberry Pi**
   - **Software**: slam_toolbox, Cartographer oder Hector SLAM
   - **Map-Persistence**: YAML-Dateien für gespeicherte Karten
   - **Nav2-Integration**: Path Planning, Localization, Obstacle Avoidance
   - **HA-Integration**: REST API für Befehle (navigate_to_pose, etc.)

3. **Hybrid-Sensorfusion**
   - **LIDAR**: Primäre Navigation
   - **Original-Sensoren**: Fallback bei LIDAR-Ausfall (z.B. starkes Sonnenlicht)
   - **Cliff-Sensoren**: Absturzerkennung (unverändert)
   - **Bumper-IR**: Kollisionserkennung (unverändert)

4. **100% Cloud-freier Betrieb**
   - **Netzwerk-Isolation**: VLAN mit Firewall (Silent Drop zu Internet)
   - **Lokales SLAM**: Alle Berechnungen auf RPi/PC
   - **HA-Dashboard**: Web-UI für Karte und Steuerung

#### **Mittlere Wahrscheinlichkeit (40-60%):**

5. **Erweiterte Sensoren**
   - **Umgebungslichtsensor** (BH1750): Adaptive Saugkraft Tag/Nacht
   - **Temperatur/Luftfeuchtigkeit** (BME280): Überhitzungserkennung
   - **Staubsensor** (GP2Y1010AU0F): Adaptive Saugkraft basierend auf Verschmutzung
   - **Beschleunigungssensor** (MPU6050): Erkennung von Festfahren/Kippen

6. **Verbesserte Akku-Intelligenz**
   - **Predictive Battery Management**: ML-basierte Vorhersage für Restlaufzeit
   - **Optimierte Pfadplanung**: Kehre zur Station zurück nur wenn nötig

7. **Multi-Floor-Support**
   - **Map-Wechsel**: Verschiedene Karten für Etagen
   - **Manuell getriggert** (LIDAR erkennt Etagen nicht automatisch ohne Lift)

#### **Niedrige Wahrscheinlichkeit (10-30%):**

8. **Computer-Vision-Integration**
   - **ESP32-CAM**: Objekt-/Haustier-Erkennung (sehr rechenintensiv)
   - **Alternative**: Externe Kamera + RPi für CV-Processing

9. **Vocale Feedback**
   - **Lautsprecher-Modul**: Text-to-Speech für Status-Updates
   - **Komplexität**: Audio-Hardware + ESP32-I2S

### Für welche Modifikationen öffnet das Projekt noch die Türen?

Das modulare Setup (ESP32 + RPi + ROS2) ermöglicht zahlreiche zukünftige Erweiterungen:

#### **🌊 Wasserstandssensor (Wischfunktion)**
- **Sensor**: Kapazitiver/Resistiver Wassersensor
- **Zweck**: 
  - Warnung bei leerem Wassertank
  - Automatisches Pausieren der Wischfunktion
  - Tracking von Wasserverbrauch
- **Integration**: Analog/I2C zu ESP32 → MQTT zu HA
- **Komplexität**: Niedrig (10-20€, 2-3h Arbeit)

#### **🗑️ Füllstandsensor Staubbehälter**
- **Sensor-Optionen**:
  - **Ultraschall** (HC-SR04): Distanzmessung zum Füllstand
  - **IR-Lichtschranke**: Erkennung von "voll"
  - **Gewichtssensor** (HX711 + Load Cell): Präzise Füllstand-Messung
- **Zweck**:
  - Push-Notification bei vollem Behälter
  - Automatisches Pausieren bis Entleerung
  - Reinigung-Effizienz-Metriken
- **Integration**: Sensor zu ESP32 → HA Automation
- **Komplexität**: Mittel (15-30€, 4-6h Arbeit + Gehäusemodifikation)

#### **🚀 Automatische "Entladestation" (Advanced)**
- **Konzept**: Staubsauger fährt zu Station, die Behälter absaugt
- **Hardware**:
  - Modifizierte Ladestation mit Absaug-Mechanismus
  - Starker Saugmotor (z.B. aus Handstaubsauger)
  - Mechanischer Adapter für Behälter-Öffnung
  - Großer Sammelbehälter (5-10L)
- **Herausforderungen**:
  - **Mechanik**: Behälter muss automatisch geöffnet werden
  - **Dichtung**: Luftdichter Anschluss für effektives Absaugen
  - **Positionierung**: Präzises Andocken (cm-genau)
- **Realismus**: 
  - **DIY-Version**: Möglich aber sehr aufwändig (100+ Stunden)
  - **Fertig-Lösung**: Würde 500-1000€ kosten (wie bei Roborock S7+)
- **Komplexität**: Sehr hoch (150-300€ Hardware, Mechanik-Know-how erforderlich)

#### **📡 Weitere Sensor-Erweiterungen**
- **mmWave-Radar** (LD2410): Präsenz-Erkennung (pausiere bei Personen im Raum)
- **UV-C-Sterilisation**: UV-LED-Strip zur Boden-Desinfektion während Reinigung
- **CO2-Sensor**: Luftqualitäts-Monitoring während Betrieb
- **Entfernungs-Sensoren** (VL53L0X Array): 360° Hinderniserkennung ohne LIDAR

#### **🧠 Software-Erweiterungen**
- **Machine Learning**: Raum-/Objektklassifizierung aus LIDAR-Daten
- **Predictive Maintenance**: Vorhersage von Verschleiß (Bürsten, Filter, Akku)
- **Occupancy-basierte Reinigung**: Lerne Bewegungsmuster der Bewohner
- **Voice Control** (lokal): Rhasspy/Mycroft-Integration ohne Cloud
- **Telegram/Matrix-Bot**: Chat-basierte Steuerung und Status-Updates

### Offene Forschungsfragen

1. **Kann der Original-G10-Mainboard-Prozessor für Custom-Code genutzt werden?**
   - UART-Output zeigt Boot-Logs, aber kein Shell-Zugriff
   - Gibt es einen JTAG/Debug-Port auf dem PCB?
   - Könnte ein Firmware-Dump via SPI-Flash-Extraktion erfolgen?

2. **Wie präzise ist die Odometrie des G10 ohne LIDAR?**
   - Gyroskop-Daten über Tuya-Protokoll abrufbar?
   - Rad-Encoder vorhanden oder nur Motor-Ansteuerung?
   - Kann Odometrie mit LIDAR fusioniert werden für besseres SLAM?

3. **Welche Tuya-Datapoints sind noch unbekannt/undokumentiert?**
   - Gibt es versteckte DPs für erweiterte Features?
   - Rohdaten von Sensoren abrufbar (Cliff, IR, Gyro)?

4. **Ist VSLAM (Visual SLAM) mit ESP32-CAM realistisch?**
   - Performance-Tests mit ORB-SLAM3 auf ESP32?
   - Oder nur als zusätzliche Input-Quelle für RPi-SLAM?

5. **Können wir die Ladestation hacken?**
   - IR-Signal-Analyse für Custom-Docking-Logik
   - Zusätzliche Ladestation-Sensoren (Kontakt-Pins)?

---

## 🛠️ Geplanter Tech-Stack

### Hardware
- **Eufy RoboVac G10 Hybrid** (Basis)
- **ESP32 DevKit** (ESP-WROOM-32, ~5€)
- **LIDAR-Modul** (Roborock LDS ~40€ oder YDLIDAR X2L ~60€)
- **Raspberry Pi 4/5** (4GB RAM, ~60-80€) – für ROS2 SLAM
- **MicroSD-Karte** (32GB+, ~8€)
- **Dupont-Kabel Set** (~3€)
- *Optional:* Zusätzliche Sensoren (siehe Erweiterungen)

**Gesamtkosten Basis-Setup:** ~120-160€

### Software
- **ESPHome** – ESP32-Firmware für lokale Integration
- **Home Assistant** – Smart-Home-Hub und UI
- **ROS2 Humble** – Robotik-Framework für SLAM
- **slam_toolbox** – 2D-SLAM-Implementierung
- **Nav2** – Navigation-Stack für Path Planning
- **RViz2** – Visualisierung (Development)
- **MQTT** – Messaging für Sensor-Daten
- **Docker** (optional) – Containerisierte ROS2-Umgebung

### Netzwerk
- **Lokales VLAN** – Isolation von Internet
- **Statische IPs** für alle Komponenten
- **Pi-hole** (optional) – DNS-Blocking für Tuya-Domains
- **WireGuard** (optional) – Sicherer Remote-Zugriff

---

## 📚 Projektstruktur (geplant)

```
eufy-robovac-g10-lidar/
├── docs/
│   ├── hardware/
│   │   ├── teardown.md              # G10 Zerlege-Anleitung mit Fotos
│   │   ├── pinout.md                # Tuya-Modul & Mainboard Pinout
│   │   ├── lidar-integration.md     # LIDAR-Modul Anschluss
│   │   └── sensor-wiring.md         # Zusätzliche Sensoren
│   ├── software/
│   │   ├── esphome-setup.md         # ESPHome Installation & Config
│   │   ├── ros2-setup.md            # ROS2 Humble auf RPi
│   │   ├── slam-configuration.md    # SLAM-Konfiguration
│   │   └── home-assistant.md        # HA-Integration
│   ├── network/
│   │   ├── firewall-rules.md        # Router-Konfiguration
│   │   └── vlan-setup.md            # Netzwerk-Isolation
│   └── research/
│       ├── tuya-protocol.md         # Tuya-Protokoll-Analyse
│       ├── datapoints.md            # Bekannte Tuya-DPs
│       └── alternatives.md          # Getestete Alternativen
├── firmware/
│   ├── esphome/
│   │   ├── eufy-g10-base.yaml       # Basis ESPHome-Config
│   │   ├── eufy-g10-lidar.yaml      # Mit LIDAR-Integration
│   │   └── secrets.yaml.example     # Template für Credentials
│   └── custom_components/
│       └── rplidar.h                # Custom C++ für RPLIDAR
├── ros2_ws/
│   ├── src/
│   │   ├── eufy_driver/             # ROS2-Package für G10-Steuerung
│   │   ├── eufy_slam/               # SLAM-Launch-Files
│   │   └── eufy_navigation/         # Nav2-Konfiguration
│   └── README.md
├── home_assistant/
│   ├── configuration.yaml           # HA-Config
│   ├── automations.yaml             # Automatisierungen
│   ├── scripts.yaml                 # Cleaning-Scripts
│   └── lovelace/
│       └── eufy-dashboard.yaml      # Custom UI
├── tools/
│   ├── tuya_key_grabber.py          # Local Key Extractor
│   ├── map_converter.py             # ROS Map → HA Format
│   └── diagnostics.sh               # System-Health-Check
├── tests/
│   ├── unit/                        # Unit-Tests
│   └── integration/                 # Hardware-Tests
├── images/                          # Fotos, Diagramme
├── LICENSE
├── README.md                        # Diese Datei
└── CHANGELOG.md
```

---

## 🚧 Aktueller Status: Research Phase

### ✅ Abgeschlossen
- [x] Literatur-Review (Rjevski-Projekte, Valetudo, LocalTuya)
- [x] Hardware-Machbarkeit analysiert
- [x] LIDAR-Optionen recherchiert
- [x] Kosten-Nutzen-Analyse
- [x] Alternative Ansätze evaluiert (ZOOZEE Z70, Dreame, etc.)

### 🔄 In Arbeit
- [ ] **Tuya-Datapoint-Mapping vervollständigen** (via Sniffer)
- [ ] **LIDAR-Hardware beschaffen** (Roborock LDS auf eBay)
- [ ] **ESP32 Test-Setup** (ESPHome-Config ohne Hardware)
- [ ] **ROS2-Testumgebung** (Docker auf Laptop)

### 📋 Geplant
- [ ] G10 Hardware-Teardown (dokumentiert mit Fotos)
- [ ] ESP32-Integration (Tuya-Modul-Ersatz)
- [ ] LIDAR-Modul physisch anschließen
- [ ] ESPHome UART-Bridge einrichten
- [ ] ROS2 auf Raspberry Pi installieren
- [ ] SLAM-Testläufe (manuelle Steuerung)
- [ ] Home Assistant Dashboard erstellen
- [ ] Hybrid-Sensorfusion implementieren
- [ ] Erste autonome Reinigung mit LIDAR
- [ ] Performance-Optimierung
- [ ] Dokumentation vervollständigen

---

## 🤝 Contributing

Dieses Projekt ist Open Source und lebt von Community-Beiträgen! Ob du:
- **Hardware-Expertise** hast (Elektronik, Mechanik, 3D-Druck)
- **Software entwickelst** (Python, C++, ROS2, ESPHome)
- **Dokumentation** verbessern möchtest
- **Testing** auf deinem eigenen G10 durchführst
- **Ideen & Feedback** teilen willst

**Jeder Beitrag ist willkommen!**

### Wie du helfen kannst:
1. **Fork** das Repository
2. Erstelle einen **Feature-Branch** (`git checkout -b feature/amazing-sensor`)
3. **Commit** deine Änderungen (`git commit -m 'Add amazing sensor integration'`)
4. **Push** zum Branch (`git push origin feature/amazing-sensor`)
5. Öffne einen **Pull Request**

### Diskussionen & Fragen:
- **GitHub Issues**: Bug-Reports, Feature-Requests, Fragen
- **GitHub Discussions**: Allgemeine Diskussionen, Ideen, Showcases
- **Reddit**: [r/homeassistant](https://reddit.com/r/homeassistant) oder [r/robotvacuums](https://reddit.com/r/robotvacuums)

---

## ⚠️ Disclaimer & Wichtige Hinweise

### ⚡ Elektrik & Sicherheit
- **Stromschlaggefahr**: Arbeite niemals am geöffneten Gerät während es an der Ladestation ist
- **Akku-Handling**: LiPo-Akkus können bei Beschädigung brennen/explodieren
- **Löten**: Bei unsachgemäßer Arbeit Verbrennungs- und Brandgefahr
- **ESD-Schutz**: Verwende ein Antistatik-Armband beim Umgang mit Elektronik

**➜ Alle Modifikationen erfolgen auf eigene Gefahr!**

### 🔧 Garantieverlust
Durch das Öffnen und Modifizieren des Geräts erlischt jegliche Herstellergarantie von Eufy/Anker. Dies ist ein **experimentelles Projekt** – es gibt keine Gewährleistung für Funktionalität oder Sicherheit.

### 📜 Rechtliches
- **Tuya-Protokoll**: Die Nutzung des lokalen Tuya-Protokolls erfolgt im Rahmen des Reverse-Engineering für Interoperabilität (§ 69e UrhG)
- **Markenrechte**: "Eufy", "Anker", "RoboVac" sind eingetragene Marken der Anker Innovations Limited. Dieses Projekt ist **nicht** offiziell unterstützt oder gesponsert.
- **Open Source Lizenzen**: Alle verwendeten Libraries (ESPHome, ROS2, etc.) unterliegen ihren jeweiligen Lizenzen

### 🔒 Privacy & Security
Dieses Projekt legt großen Wert auf **Datenschutz und lokale Kontrolle**:
- ✅ **Keine Cloud-Verbindungen** nach erfolgreicher Einrichtung
- ✅ **Alle Daten bleiben lokal** (Maps, Logs, Telemetrie)
- ✅ **Keine Tracking-Software** (Tuya-Cloud wird blockiert)
- ⚠️ **Eigene Verantwortung**: Sichere dein lokales Netzwerk ab (Firewall, VPN, starke Passwörter)

### 🌱 Nachhaltigkeit
Ziel dieses Projekts ist es, **vorhandene Hardware weiterzuverwenden** und durch Software-Upgrades zu verbessern, anstatt neue Geräte zu kaufen. Das ist:
- ♻️ Ressourcenschonend (kein E-Waste)
- 💰 Kosteneffizient (~100€ statt 300-500€ für neuen Premium-Roboter)
- 🧠 Bildungsreich (Hands-on mit Robotik & IoT)

---

## 🔗 Verwandte Ressourcen & Community-Projekte

### Eufy Hacking Ressourcen

- **[Hacking my Eufy Robot Vac](https://maxdiamond.co.uk/blog/hacking-my-eufy-robot-vac)** von Max Diamond
  - Detaillierte Anleitung zur Extraktion von Tuya Local Keys
  - Nutzt das Python-Paket `eufy-clean-local-key-grabber` zur Key-Extraktion
  - Dokumentiert TuyAPI-Integrations-Herausforderungen

- **[eufy-robovac](https://www.npmjs.com/package/eufy-robovac)** - Node.js Library mit TuyAPI für lokale Eufy-Steuerung

- **[Make-Eufy-Smart-Again](https://github.com/nakulbende/Make-Eufy-Smart-Again)** - ESP8266-basierte IR-Fernbedienung über Web-Interface

- **[robovac-hack](https://github.com/msgageek/robovac-hack)** - DIY-Navigations-Modifikationen für Eufy 11 Serie

### Wichtige technische Erkenntnisse

| Thema | Details |
|-------|---------|
| **Protokoll** | Tuya-basierte Kommunikation |
| **Benötigte Keys** | Device ID + Local Key für lokale Steuerung |
| **Key-Extraktion** | `eufy-clean-local-key-grabber` (Python) |
| **Kommunikation** | TuyAPI-Protokoll über lokales Netzwerk |

> **Hinweis:** Diese Ressourcen zielen auf verschiedene Eufy-Modelle ab, aber die zugrundeliegende Tuya-Architektur ist produktübergreifend ähnlich und liefert wertvolle Einblicke für Reverse Engineering.

---

## 📄 Lizenz

Dieses Projekt ist lizenziert unter der **MIT License** – siehe [LICENSE](LICENSE) für Details.

**TLDR:** Du darfst den Code verwenden, modifizieren, verbreiten – kommerziell oder privat – solange du die Copyright-Notice beibehältst. Keine Gewährleistung.

---

## 🙏 Danksagungen

Dieses Projekt steht auf den Schultern von Giganten:

- **[Rjevski](https://github.com/Rjevski)** – Für die Pionierarbeit beim G10-Hacking und die exzellente Dokumentation
- **[Valetudo](https://valetudo.cloud)** (Hypfer) – Inspiration für Cloud-freie Roboter-Steuerung
- **[Dennis Giese](https://dontvacuum.me)** – Für Dustbuilder und die Roborock/Dreame-Community
- **[ESPHome](https://esphome.io)** – Für die beste lokale IoT-Plattform
- **[Home Assistant](https://www.home-assistant.io)** – Für das Open-Source Smart-Home-Framework
- **[ROS2 Community](https://www.ros.org)** – Für die unglaublichen Robotik-Tools
- **LocalTuya Contributors** – Für die Tuya-Reverse-Engineering-Arbeit

Und allen Community-Mitgliedern, die Wissen teilen, testen, dokumentieren und debuggen! 🚀

---

## 📞 Kontakt & Support

- **GitHub Issues**: [github.com/mleem97/eufy-robovac-g10-lidar/issues](https://github.com/mleem97/eufy-robovac-g10-lidar/issues)
- **Project Owner**: [@mleem97](https://github.com/mleem97)

---

<p align="center">
  <strong>Made with ❤️ for the Open Source Robotics Community</strong><br>
  <em>Turning "dumb" robots into smart ones, one ESP32 at a time.</em>
</p>

---

## 📊 Project Statistics

![GitHub stars](https://img.shields.io/github/stars/mleem97/eufy-robovac-g10-lidar?style=social)
![GitHub forks](https://img.shields.io/github/forks/mleem97/eufy-robovac-g10-lidar?style=social)
![GitHub issues](https://img.shields.io/github/issues/mleem97/eufy-robovac-g10-lidar)
![GitHub last commit](https://img.shields.io/github/last-commit/mleem97/eufy-robovac-g10-lidar)

---

**⚡ Status**: Research Phase – Stay tuned for updates!
