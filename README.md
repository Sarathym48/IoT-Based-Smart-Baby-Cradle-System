#  IoT-Based Smart Baby Cradle System

![IoT](https://img.shields.io/badge/IoT-ESP32-blue)
![Cloud](https://img.shields.io/badge/App-Blynk-green)
![Status](https://img.shields.io/badge/Stage-Prototype%20Complete-brightgreen)

##  Project Overview

This project showcases an **IoT-powered Smart Baby Cradle System** designed to provide enhanced infant care with minimal human intervention. The system combines real-time monitoring, automated cradle swinging, cry detection, and hygiene alerts using a network of smart sensors and cloud-based control.

By integrating **ESP32 microcontrollers**, **Blynk IoT platform**, and **ESP32-CAM**, the system offers parents and caregivers remote access and live feedback for baby safety, comfort, and convenience.

---

##  Key Objectives

- Enable **automated swinging** of the cradle in response to baby’s cry.
- Provide **real-time video streaming** using ESP32-CAM.
- Detect **baby movement** using a PIR sensor.
- Alert caregivers about **urine wetness** using a modified rain sensor.
- Allow full remote control and monitoring via the **Blynk IoT mobile app**.
- Display system status on an **OLED screen** for local feedback.

---

##  Hardware Components

| Component              | Description                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| ESP32-S                | Core microcontroller for control and connectivity.                          |
| ESP32-CAM              | Camera module for real-time video streaming.                                |
| LM393 Sound Sensor     | Detects crying sounds to trigger swing.                                     |
| PIR Motion Sensor      | Detects baby’s motion or presence.                                          |
| Rain Sensor            | Used to detect urine wetness under the baby.                                |
| Servo Motor            | Swings the cradle automatically.                                            |
| OLED Display           | Displays cradle status messages.                                            |
| Power Supply Unit      | Provides regulated 5V DC to components.                                     |

---

##  Software & Tools

- **Arduino IDE** – To program ESP32 modules in Embedded C.
- **Blynk IoT Platform** – For mobile control and cloud monitoring.
- **ESP32 Libraries** – Includes WiFi, Servo, OLED, and Blynk libraries.
- **Serial Monitor** – For debugging and serial data display.

---

##  Features

-  **Live Monitoring** – Video feed using ESP32-CAM.
-  **Cry Detection** – Triggers swing when sound is detected.
-  **Motion Sensing** – Detects baby's movement via PIR.
-  **Wetness Alert** – Notifies when urine is detected (Rain sensor).
-  **Mobile App Control** – Cradle can be controlled remotely via Blynk.
-  **OLED Display** – Shows system feedback like "Swinging", "Crying Detected", or "Wetness Alert".

---

##  Workflow

```mermaid
graph TD;
    Start[Power On] --> Init[Initialize Sensors & WiFi]
    Init --> Monitor[Monitor Baby: PIR + Sound + Rain Sensor]
    Monitor --> CheckCry{Cry Detected?}
    CheckCry -- Yes --> Swing[Activate Cradle Swing]
    CheckCry -- No --> CheckMotion{Motion Detected?}
    CheckMotion -- Yes --> StatusDisplay[Show Baby Moving]
    Monitor --> CheckWetness{Wetness Detected?}
    CheckWetness -- Yes --> Alert[Send Blynk Alert & Show on OLED]
    Swing --> SendStatus[Update Blynk Status]
    StatusDisplay --> Loop[Repeat Monitoring]
    Alert --> Loop
    SendStatus --> Loop

