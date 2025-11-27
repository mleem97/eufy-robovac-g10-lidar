# 🤖 Eufy RoboVac G10 LIDAR Upgrade Project

![Status](https://img.shields.io/badge/Status-Research%20Phase-yellow)
![License](https://img.shields.io/badge/License-MIT-blue)
![Hardware](https://img.shields.io/badge/Hardware-ESP32%20%7C%20LIDAR-orange)
![Privacy](https://img.shields.io/badge/Privacy-Cloud%20Free-green)

> **Upgrading a "dumb" vacuum robot to a smart, locally-controlled cleaning machine with real SLAM capabilities**

An open-source project to transform the Eufy RoboVac G10 Hybrid from a simple gyroscope-based random cleaner into an intelligent, LIDAR-controlled robot with real room mapping and 100% local control – without any cloud dependencies.

---

## 📖 Introduction

### Motivation

I'm **fed up** with my RoboVac racing through my apartment like a lunatic using the "eyes closed and go" strategy. The G10 Hybrid simply isn't smart – it cleans in **uneven patterns**, drives over the same spots multiple times while completely ignoring others, and regularly loses orientation.

**Otherwise, I'm satisfied with the basic performance:** The suction power is okay, the mopping function works, and the hardware is solid. This led me to the question: **What else can I improve on this thing?**

During my research, I discovered the excellent project by **Rjevski**:
- [esphome-eufy-robovac-g10-hybrid](https://github.com/Rjevski/esphome-eufy-robovac-g10-hybrid) – ESPHome integration for replacing the Tuya module
- [ha-eufy-robovac-g10-hybrid](https://github.com/Rjevski/ha-eufy-robovac-g10-hybrid) – Home Assistant integration

These projects demonstrate that the G10 is **modifiable** and that the proprietary Tuya module can be replaced with an **ESP32 running ESPHome**. This opens the door for real smart home integration – **without cloud, without Anker/Eufy servers, completely local**.

### Why Make It Smarter?

1. **Intelligent Navigation Instead of Random**: Real LIDAR-based SLAM (Simultaneous Localization and Mapping) for systematic room coverage
2. **Privacy First**: No connection to Eufy/Anker/Tuya servers – 100% local control over all data
3. **Extensibility**: Open platform for additional sensors, custom software, and automations
4. **Learning Project**: Hands-on experience with robotics, SLAM, ROS2, ESP32/ESPHome, and IoT security
5. **Sustainability**: Upgrade existing hardware instead of buying new

**The Goal:** Transform a €150 budget robot into a premium robot with features like Roborock/Xiaomi (€300-500) – for under €100 in additional hardware.

---

## 🎯 Thesis / Theory

### Expected State After Implementation

After successful implementation, the Eufy RoboVac G10 Hybrid will have the following capabilities:

#### ✅ **Primary Goals:**
- **360° LIDAR scanning** for real room mapping (8-12m range)
- **Real-time mapping** of the apartment with persistent maps
- **Intelligent path planning** – no more random patterns
- **Zone cleaning** – "Clean only the kitchen"
- **No-go zones** – defined areas to be avoided
- **"Go to position X,Y"** – direct navigation to points
- **Fully local control** via Home Assistant (no internet required)
- **Hybrid navigation**: LIDAR as primary, original sensors (IR/bumper) as fallback

#### ⚙️ **Secondary Goals:**
- **Live visualization** of the map in Home Assistant/RViz
- **Improved battery management** – optimized return to charging station
- **Room recognition** – "Living room", "Kitchen", "Bedroom"
- **Cleaning statistics** – area, time, coverage
- **Custom automations** (e.g., "Clean when nobody is home")

### What We Already Know About Modifying the G10

Thanks to the groundwork by **Rjevski** and independent research:

#### **Hardware Architecture:**
- **CPU**: Amicro ARM-based microcontroller (proprietary, no shell access)
- **WiFi Module**: Tuya-compatible module with **4 twisted wires**:
  - Red: 3.3V
  - Green: GND
  - Blue: RX (to mainboard TX)
  - Black: TX (to mainboard RX)
- **UART Communication**: 115200 baud, 8N1
- **Tuya Protocol**: Documented protocol for control
- **Sensors**: Gyroscope, IR bumper, cliff sensors (4x drop detection)

#### **Proven Modifications:**
1. ✅ **Tuya Module Replacement**: ESP8266/ESP32 can function as drop-in replacement
2. ✅ **ESPHome Integration**: Stable UART communication with original firmware
3. ✅ **Home Assistant Control**: Full control over local network
4. ✅ **Datapoint Mapping**: Tuya DPs known (Power, Mode, Fan Speed, Battery, etc.)
5. ✅ **OTA Updates**: Over-the-air firmware updates via ESPHome possible

#### **Known Limitations:**
- ⚠️ **No root access** to Amicro CPU (no known exploit)
- ⚠️ **Original firmware required**: ESP32 acts as proxy, not replacement
- ⚠️ **Limited GPIO pins** on ESP32 (UART for G10, additional for LIDAR/sensors)

### What Is Theoretically Feasible?

#### **High Probability (>80%):**

1. **LIDAR Integration via ESP32**
   - **Hardware**: Roborock/Xiaomi LDS module (€40-90) or YDLIDAR X2L (~€60)
   - **Connection**: ESP32 → 2nd UART for LIDAR data
   - **Protocol**: Well documented (RPLIDAR/SLAMTEC compatible)
   - **Streaming**: TCP/IP stream to Raspberry Pi for SLAM processing

2. **ROS2 SLAM on Raspberry Pi**
   - **Software**: slam_toolbox, Cartographer, or Hector SLAM
   - **Map Persistence**: YAML files for saved maps
   - **Nav2 Integration**: Path planning, localization, obstacle avoidance
   - **HA Integration**: REST API for commands (navigate_to_pose, etc.)

3. **Hybrid Sensor Fusion**
   - **LIDAR**: Primary navigation
   - **Original Sensors**: Fallback when LIDAR fails (e.g., bright sunlight)
   - **Cliff Sensors**: Drop detection (unchanged)
   - **Bumper IR**: Collision detection (unchanged)

4. **100% Cloud-Free Operation**
   - **Network Isolation**: VLAN with firewall (silent drop to internet)
   - **Local SLAM**: All calculations on RPi/PC
   - **HA Dashboard**: Web UI for map and control

#### **Medium Probability (40-60%):**

5. **Extended Sensors**
   - **Ambient Light Sensor** (BH1750): Adaptive suction power day/night
   - **Temperature/Humidity** (BME280): Overheat detection
   - **Dust Sensor** (GP2Y1010AU0F): Adaptive suction based on dirt level
   - **Accelerometer** (MPU6050): Detection of stuck/tilted states

6. **Improved Battery Intelligence**
   - **Predictive Battery Management**: ML-based remaining runtime prediction
   - **Optimized Path Planning**: Return to station only when necessary

7. **Multi-Floor Support**
   - **Map Switching**: Different maps for floors
   - **Manually triggered** (LIDAR doesn't auto-detect floors without elevator)

#### **Low Probability (10-30%):**

8. **Computer Vision Integration**
   - **ESP32-CAM**: Object/pet detection (very compute-intensive)
   - **Alternative**: External camera + RPi for CV processing

9. **Voice Feedback**
   - **Speaker Module**: Text-to-speech for status updates
   - **Complexity**: Audio hardware + ESP32 I2S

### What Future Modifications Does This Project Enable?

The modular setup (ESP32 + RPi + ROS2) enables numerous future extensions:

#### **🌊 Water Level Sensor (Mopping Function)**
- **Sensor**: Capacitive/resistive water sensor
- **Purpose**: 
  - Warning when water tank is empty
  - Automatic pause of mopping function
  - Track water consumption
- **Integration**: Analog/I2C to ESP32 → MQTT to HA
- **Complexity**: Low (€10-20, 2-3h work)

#### **🗑️ Dust Bin Fill Level Sensor**
- **Sensor Options**:
  - **Ultrasonic** (HC-SR04): Distance measurement to fill level
  - **IR Light Barrier**: Detection of "full" state
  - **Weight Sensor** (HX711 + Load Cell): Precise fill level measurement
- **Purpose**:
  - Push notification when bin is full
  - Automatic pause until emptied
  - Cleaning efficiency metrics
- **Integration**: Sensor to ESP32 → HA automation
- **Complexity**: Medium (€15-30, 4-6h work + case modification)

#### **🚀 Automatic "Emptying Station" (Advanced)**
- **Concept**: Vacuum drives to station that empties the bin
- **Hardware**:
  - Modified charging station with suction mechanism
  - Powerful suction motor (e.g., from handheld vacuum)
  - Mechanical adapter for bin opening
  - Large collection container (5-10L)
- **Challenges**:
  - **Mechanics**: Bin must open automatically
  - **Sealing**: Airtight connection for effective suction
  - **Positioning**: Precise docking (cm accuracy)
- **Realism**: 
  - **DIY Version**: Possible but very labor-intensive (100+ hours)
  - **Off-the-shelf Solution**: Would cost €500-1000 (like Roborock S7+)
- **Complexity**: Very high (€150-300 hardware, mechanical know-how required)

#### **📡 Additional Sensor Extensions**
- **mmWave Radar** (LD2410): Presence detection (pause when people in room)
- **UV-C Sterilization**: UV LED strip for floor disinfection during cleaning
- **CO2 Sensor**: Air quality monitoring during operation
- **Distance Sensors** (VL53L0X array): 360° obstacle detection without LIDAR

#### **🧠 Software Extensions**
- **Machine Learning**: Room/object classification from LIDAR data
- **Predictive Maintenance**: Prediction of wear (brushes, filters, battery)
- **Occupancy-Based Cleaning**: Learn movement patterns of residents
- **Voice Control** (local): Rhasspy/Mycroft integration without cloud
- **Telegram/Matrix Bot**: Chat-based control and status updates

### Open Research Questions

1. **Can the original G10 mainboard processor be used for custom code?**
   - UART output shows boot logs, but no shell access
   - Is there a JTAG/debug port on the PCB?
   - Could firmware dump via SPI flash extraction work?

2. **How accurate is the G10's odometry without LIDAR?**
   - Gyroscope data retrievable via Tuya protocol?
   - Wheel encoders present or only motor control?
   - Can odometry be fused with LIDAR for better SLAM?

3. **Which Tuya datapoints are still unknown/undocumented?**
   - Are there hidden DPs for extended features?
   - Raw data from sensors retrievable (cliff, IR, gyro)?

4. **Is VSLAM (Visual SLAM) with ESP32-CAM realistic?**
   - Performance tests with ORB-SLAM3 on ESP32?
   - Or only as additional input source for RPi SLAM?

5. **Can we hack the charging station?**
   - IR signal analysis for custom docking logic
   - Additional charging station sensors (contact pins)?

---

## 🛠️ Planned Tech Stack

### Hardware
- **Eufy RoboVac G10 Hybrid** (base)
- **ESP32 DevKit** (ESP-WROOM-32, ~€5)
- **LIDAR Module** (Roborock LDS ~€40 or YDLIDAR X2L ~€60)
- **Raspberry Pi 4/5** (4GB RAM, ~€60-80) – for ROS2 SLAM
- **MicroSD Card** (32GB+, ~€8)
- **Dupont Cable Set** (~€3)
- *Optional:* Additional sensors (see extensions)

**Total Cost Base Setup:** ~€120-160

### Software
- **ESPHome** – ESP32 firmware for local integration
- **Home Assistant** – Smart home hub and UI
- **ROS2 Humble** – Robotics framework for SLAM
- **slam_toolbox** – 2D SLAM implementation
- **Nav2** – Navigation stack for path planning
- **RViz2** – Visualization (development)
- **MQTT** – Messaging for sensor data
- **Docker** (optional) – Containerized ROS2 environment

### Network
- **Local VLAN** – Isolation from internet
- **Static IPs** for all components
- **Pi-hole** (optional) – DNS blocking for Tuya domains
- **WireGuard** (optional) – Secure remote access

---

## 📚 Project Structure (Planned)

```
eufy-robovac-g10-lidar/
├── docs/
│   ├── hardware/
│   │   ├── teardown.md              # G10 disassembly guide with photos
│   │   ├── pinout.md                # Tuya module & mainboard pinout
│   │   ├── lidar-integration.md     # LIDAR module connection
│   │   └── sensor-wiring.md         # Additional sensors
│   ├── software/
│   │   ├── esphome-setup.md         # ESPHome installation & config
│   │   ├── ros2-setup.md            # ROS2 Humble on RPi
│   │   ├── slam-configuration.md    # SLAM configuration
│   │   └── home-assistant.md        # HA integration
│   ├── network/
│   │   ├── firewall-rules.md        # Router configuration
│   │   └── vlan-setup.md            # Network isolation
│   └── research/
│       ├── tuya-protocol.md         # Tuya protocol analysis
│       ├── datapoints.md            # Known Tuya DPs
│       └── alternatives.md          # Tested alternatives
├── firmware/
│   ├── esphome/
│   │   ├── eufy-g10-base.yaml       # Base ESPHome config
│   │   ├── eufy-g10-lidar.yaml      # With LIDAR integration
│   │   └── secrets.yaml.example     # Template for credentials
│   └── custom_components/
│       └── rplidar.h                # Custom C++ for RPLIDAR
├── ros2_ws/
│   ├── src/
│   │   ├── eufy_driver/             # ROS2 package for G10 control
│   │   ├── eufy_slam/               # SLAM launch files
│   │   └── eufy_navigation/         # Nav2 configuration
│   └── README.md
├── home_assistant/
│   ├── configuration.yaml           # HA config
│   ├── automations.yaml             # Automations
│   ├── scripts.yaml                 # Cleaning scripts
│   └── lovelace/
│       └── eufy-dashboard.yaml      # Custom UI
├── tools/
│   ├── tuya_key_grabber.py          # Local key extractor
│   ├── map_converter.py             # ROS map → HA format
│   └── diagnostics.sh               # System health check
├── tests/
│   ├── unit/                        # Unit tests
│   └── integration/                 # Hardware tests
├── images/                          # Photos, diagrams
├── LICENSE
├── README.md                        # This file
└── CHANGELOG.md
```

---

## 🚧 Current Status: Research Phase

### ✅ Completed
- [x] Literature review (Rjevski projects, Valetudo, LocalTuya)
- [x] Hardware feasibility analyzed
- [x] LIDAR options researched
- [x] Cost-benefit analysis
- [x] Alternative approaches evaluated (ZOOZEE Z70, Dreame, etc.)

### 🔄 In Progress
- [ ] **Complete Tuya datapoint mapping** (via sniffer)
- [ ] **Acquire LIDAR hardware** (Roborock LDS on eBay)
- [ ] **ESP32 test setup** (ESPHome config without hardware)
- [ ] **ROS2 test environment** (Docker on laptop)

### 📋 Planned
- [ ] G10 hardware teardown (documented with photos)
- [ ] ESP32 integration (Tuya module replacement)
- [ ] LIDAR module physical connection
- [ ] ESPHome UART bridge setup
- [ ] ROS2 installation on Raspberry Pi
- [ ] SLAM test runs (manual control)
- [ ] Home Assistant dashboard creation
- [ ] Hybrid sensor fusion implementation
- [ ] First autonomous cleaning with LIDAR
- [ ] Performance optimization
- [ ] Complete documentation

---

## 🤝 Contributing

This project is open source and thrives on community contributions! Whether you:
- Have **hardware expertise** (electronics, mechanics, 3D printing)
- **Develop software** (Python, C++, ROS2, ESPHome)
- Want to **improve documentation**
- Can perform **testing** on your own G10
- Want to share **ideas & feedback**

**Every contribution is welcome!**

### How You Can Help:
1. **Fork** the repository
2. Create a **feature branch** (`git checkout -b feature/amazing-sensor`)
3. **Commit** your changes (`git commit -m 'Add amazing sensor integration'`)
4. **Push** to the branch (`git push origin feature/amazing-sensor`)
5. Open a **Pull Request**

### Discussions & Questions:
- **GitHub Issues**: Bug reports, feature requests, questions
- **GitHub Discussions**: General discussions, ideas, showcases
- **Reddit**: [r/homeassistant](https://reddit.com/r/homeassistant) or [r/robotvacuums](https://reddit.com/r/robotvacuums)

---

## ⚠️ Disclaimer & Important Notes

### ⚡ Electrical & Safety
- **Electric shock hazard**: Never work on an open device while it's on the charging station
- **Battery handling**: LiPo batteries can burn/explode if damaged
- **Soldering**: Risk of burns and fire with improper work
- **ESD protection**: Use an anti-static wristband when handling electronics

**➜ All modifications are at your own risk!**

### 🔧 Warranty Void
Opening and modifying the device voids any manufacturer warranty from Eufy/Anker. This is an **experimental project** – there is no guarantee of functionality or safety.

### 📜 Legal
- **Tuya Protocol**: Use of the local Tuya protocol is done within the framework of reverse engineering for interoperability (§ 69e UrhG / DMCA exemptions)
- **Trademarks**: "Eufy", "Anker", "RoboVac" are registered trademarks of Anker Innovations Limited. This project is **not** officially supported or sponsored.
- **Open Source Licenses**: All used libraries (ESPHome, ROS2, etc.) are subject to their respective licenses

### 🔒 Privacy & Security
This project places great emphasis on **privacy and local control**:
- ✅ **No cloud connections** after successful setup
- ✅ **All data stays local** (maps, logs, telemetry)
- ✅ **No tracking software** (Tuya cloud is blocked)
- ⚠️ **Own responsibility**: Secure your local network (firewall, VPN, strong passwords)

### 🌱 Sustainability
The goal of this project is to **reuse existing hardware** and improve it through software upgrades instead of buying new devices. This is:
- ♻️ Resource-friendly (no e-waste)
- 💰 Cost-effective (~€100 instead of €300-500 for new premium robot)
- 🧠 Educational (hands-on with robotics & IoT)

---

## 📄 License

This project is licensed under the **MIT License** – see [LICENSE](LICENSE) for details.

**TLDR:** You may use, modify, distribute the code – commercially or privately – as long as you retain the copyright notice. No warranty provided.

---

## 🙏 Acknowledgments

This project stands on the shoulders of giants:

- **[Rjevski](https://github.com/Rjevski)** – For pioneering work on G10 hacking and excellent documentation
- **[Valetudo](https://valetudo.cloud)** (Hypfer) – Inspiration for cloud-free robot control
- **[Dennis Giese](https://dontvacuum.me)** – For Dustbuilder and the Roborock/Dreame community
- **[ESPHome](https://esphome.io)** – For the best local IoT platform
- **[Home Assistant](https://www.home-assistant.io)** – For the open source smart home framework
- **[ROS2 Community](https://www.ros.org)** – For the incredible robotics tools
- **LocalTuya Contributors** – For the Tuya reverse engineering work

And all community members who share knowledge, test, document, and debug! 🚀

---

## 📞 Contact & Support

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
