# IoT-Based Mine Safety Device

## 📌 Overview
The *IoT-Based Mine Safety Device* is designed to *monitor the health and environmental conditions* of mine workers in real-time. It uses *gas sensors, heart rate sensors, and wireless communication* to detect hazardous conditions and trigger alerts to prevent accidents.

## 🚀 Features
- 📡 *Real-time Monitoring*: Tracks gas levels (e.g., CO, CH₄) and worker heart rate.
- ⚠ *Smart Alerts*: Sends warnings via WiFi/Lora when conditions exceed safety thresholds.
- 📊 *Live Dashboard*: Displays data and historical trends using a web-based interface.
- 🔔 *Emergency Notifications*: Triggers alarms for high-risk situations.

## 🛠 Tech Stack
- *Hardware:*
  - ESP32 (Microcontroller)
  - MQ2 Gas Sensor (Smoke, CO, CH₄ Detection)
  - Pulse Sensor (Heart Rate Monitoring)
  - WIFI- *Software:*
  - Arduino IDE (Programming)
  - Firebase (Cloud Database)
  - HTML, JavaScript (Dashboard)

## 🔗 System Architecture
1. *ESP32* reads sensor data.
2. Data is processed & compared with threshold values.
3. Alerts are triggered if unsafe conditions are detected.
4. Sensor data is sent to *Firebase* and *Web Dashboard*.
5. Alerts are communicated via *WiFi/Lora* to the control room.

## ⚙ Installation & Setup
1. *Flash the ESP32* with the provided Arduino code.
2. *Connect Sensors*:
   - Gas Sensor → ESP32 (Analog Input)
   - Heart Rate Sensor → ESP32 (Analog Input)
3. *Set up WiFi Credentials* in the code.
4. *Deploy Web Dashboard* (hosted on ESP32).
5. *(Optional) Integrate Firebase* for remote monitoring.

## 🚨 Safety & Compliance
- Designed with *Intrinsically Safe Electronics* to prevent sparks in hazardous environments.
- Ensures *data security & redundancy* for reliable alert transmission.

## 💰 Estimated Cost (Large Scale Production)
- Single Device: ₹3,000 - ₹5,000
- Large-Scale Deployment (~1000 units): ₹25-30 Lakhs (including cloud infrastructure & maintenance)

## 🔮 Future Scope
- *AI-powered risk prediction* using historical data.
- *GPS Tracking* for worker location monitoring.
- *5G Integration* for ultra-low latency communication.
- *Multi-Gas Sensors* for detecting multiple hazardous gases.

## 🏆 Contributors
- *Project Lead*: Suriya K
- *Hardware Team*: Mugil Varnan M
- *Software Development*: Kiruthikha R , Keerthana R

## 📜 License
This project is open-source under the *MIT License*.

---
🚀 Ensuring miner safety with cutting-edge IoT technology!