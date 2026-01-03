# UFMFV8-15-3: Wireless Teleoperation & Automation System for AL5D

## 📋 Project Overview
This repository contains the firmware and documentation for a custom "Fly-by-Wire" control system designed for the **Lynxmotion AL5D** robotic arm. Developed as part of the *Group Design and Integration Project* (UFMFV8-15-3), this system replaces the stock SSC-32u controller with a distributed ESP32/ESP8266 architecture.

The system features real-time **inverse kinematic coupling** for payload leveling, **dynamic center-of-gravity compensation** (Anti-Topple), and a rigorous **safety interlock** system to meet industrial operation standards.

## 🏗 System Architecture

The project is divided into two distinct firmware modules:

### 1. The Hub (Mission Control)
* **Hardware:** ESP32-S3 DevKitC-1
* **Role:** Human-Machine Interface (HMI) & Physics Engine.
* **Function:** Reads analog inputs, performs kinematic math, manages the safety state machine, and transmits normalized pulse data via ESP-NOW.
* **Key Feature:** "Ghost Variable" physics engine that calculates stability offsets without corrupting state memory.

### 2. The Robot (Actuator)
* **Hardware:** NodeMCU V3 (ESP8266) + PCA9685 16-Channel Servo Driver.
* **Role:** Motor Driver & Safety Slew Limiter.
* **Function:** Receives wireless packets, applies motion smoothing (Slew-Rate Limiting) to prevent mechanical stress, and drives the servos via I2C.
* **Safety:** Implements a Safe-Copy protocol (`memcpy`) to prevent memory alignment faults during operation.

## 🌟 Key Features

* **🛡️ Safety First:**
    * **Pre-Flight Interlock:** System prevents motor engagement until sensors are tared and drift is verified to be <5%.
    * **Watchdog:** Automatic safe-state shutdown if wireless link is lost for >2 seconds.
* **🧠 "Fly-By-Wire" Physics:**
    * **Anti-Topple:** Automatically leans the shoulder backward as the elbow extends to maintain the center of gravity within the base footprint.
    * **Wrist Leveling:** Kinematically couples the wrist pitch to the shoulder and elbow angles, keeping the gripper parallel to the ground automatically.
* **🎮 Teleoperation:**
    * Low-latency ESP-NOW peer-to-peer protocol (<5ms latency).
    * Exponential smoothing on inputs for precision control.
* **📼 Teach & Repeat:**
    * Record waypoint sequences in real-time.
    * Autonomous replay capability for repetitive tasks.

## 🔌 Hardware Bill of Materials (BOM)

| Component | Specification | Role |
| :--- | :--- | :--- |
| **Controller** | ESP32-S3 DevKitC-1 | Primary Computing |
| **Display** | 1602 LCD (I2C) | Status & Drift Feedback |
| **Input** | 2x Analog Joysticks | 4-Axis Control |
| **Receiver** | NodeMCU V3 (ESP8266) | Wireless Receiver |
| **Driver** | PCA9685 | 16-Channel PWM Driver |
| **Power** | 5V 10A PSU | Servo Power Rail |
| **Arm** | Lynxmotion AL5D | Mechanical Platform |

## 🕹️ Controls

| Input | Action |
| :--- | :--- |
| **Left Stick X** | Elbow Up/Down |
| **Left Stick Y** | Head Rotation (Base Spin) |
| **Right Stick X** | Base Rotation |
| **Right Stick Y** | Shoulder Forward/Back (Inverted) |
| **Left Click** | Open Gripper |
| **Right Click** | Close Gripper |
| **Btn 1 (Sel/Rec)** | Confirm Safety / Record Waypoint |
| **Btn 2 (Play)** | Playback Sequence |
| **Btn 3 (Diag)** | Toggle Diagnostics Overlay |

## 🚀 Installation & Build

This project is built using **PlatformIO** in VS Code.

### Dependencies
* `LiquidCrystal_I2C` (Hub)
* `Adafruit PWM Servo Driver Library` (Robot)
* `ESP-NOW` (Built-in)
* `WiFi` (Built-in)

### Setup Instructions
1.  **Clone the Repo:**
    ```bash
    git clone [https://github.com/Crossfire-13/Key_Grabber_Project.git](https://github.com/Crossfire-13/Key_Grabber_Project.git)
    ```
2.  **Flash the Robot:**
    * Open `src/Robot` in PlatformIO.
    * Connect NodeMCU via USB.
    * Upload.
3.  **Flash the Hub:**
    * Open `src/Hub` in PlatformIO.
    * Connect ESP32-S3 via USB.
    * Upload.
4.  **Boot Sequence:**
    * Power on Robot (Wait for blue LED).
    * Power on Hub.
    * **DO NOT TOUCH STICKS.** Allow system to Auto-Tare.
    * Confirm Drift is `<200` on LCD.
    * Press **Btn 1** to engage drives.

## 📄 License & Credits
* **Course:** UFMFV8-15-3 Group Design and Integration Project.
* **Institution:** UWE Bristol.
* **Authors:** Crossfire-13 & Team.

---
*Note: This system has been verified for safety and compliance with the 2025-26 Project Brief requirements.*
