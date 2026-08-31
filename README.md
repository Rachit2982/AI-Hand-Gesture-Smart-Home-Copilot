# AI Hand Gesture Smart Home Copilot

A smart home control system that enables users to operate connected devices using hand gestures and a mobile-friendly web dashboard.

## Overview

AI Hand Gesture Smart Home Copilot is designed to give users more flexible control over a home environment. Instead of relying on a single interaction method, the system supports both gesture-based control and manual dashboard control.

The project is built around a simple principle: sensor data should provide context, not automatically trigger actions. For example, a high room temperature is displayed to the user, but a fan is only turned on when the user explicitly chooses to do so or when a configured automation is enabled.

This first version focuses on delivering a reliable working system within the project timeline. More advanced features such as speech recognition can be considered in a future version.

## Project Roadmap

![Project Roadmap](mini_project_mind_map.png)

## Goals and Scope

### Version 1 features

- Hand gesture recognition through a camera
- Smart device control using an ESP32
- Mobile-friendly web dashboard
- Manual device control from a phone
- MQTT communication between backend and ESP32
- Basic environmental monitoring with temperature and humidity
- Activity and device status monitoring
- Basic context-aware recommendations

### Core objective

The main goal of Version 1 is to build a complete and reliable pipeline from user input to physical device control.

## System Architecture

The system supports two primary input paths:

### 1. Gesture-based control

The user performs a predefined gesture in front of the camera.

```text
User Hand
  ↓
Camera
  ↓
OpenCV
  ↓
MediaPipe Hands
  ↓
Gesture Recognition
  ↓
Backend
  ↓
MQTT
  ↓
ESP32
  ↓
Connected Device
```

The camera captures video frames, MediaPipe detects the hand and landmarks, and the gesture recognition module classifies the gesture. The backend validates the action and sends the corresponding command through MQTT to the ESP32, which then switches the connected device.

### 2. Dashboard-based control

The user can also control devices manually from a phone or browser-based dashboard.

```text
Phone Browser
  ↓
Web Dashboard
  ↓
Backend API
  ↓
MQTT
  ↓
ESP32
  ↓
Connected Device
```

This mode is especially useful when the user is not near the camera or prefers direct manual control.

> During development, the phone and backend machine should be on the same local network. The phone should access the backend using the computer's local IP address rather than localhost.

## Gesture Control Design

Version 1 uses a small number of reliable gestures instead of trying to support a large gesture set.

| Gesture | Purpose | Example Action |
| --- | --- | --- |
| Open Palm | Shutdown / Leaving Mode | Turn selected devices off |
| Closed Fist | Activate | Turn a selected device or mode on |
| Thumb Up | Confirm | Confirm a pending action |
| Victory Sign | Work Mode | Activate the user's work setup |

These gestures are intentionally simple and easy to recognize. The exact mapping can be adjusted during development, but every gesture should have a clear, specific meaning.

To improve reliability, the system should not execute actions when a gesture is uncertain or below the required confidence threshold.

## Sensor Data and Context Awareness

Sensors provide environmental information, but they should not automatically trigger device actions unless the user has set up a valid automation rule.

### Primary sensor for Version 1

- DHT22

The DHT22 measures:

- Temperature
- Humidity

Example:

- Temperature: 34°C
- Humidity: 60%

This data can be displayed in the dashboard and used as context for recommendations.

Example flow:

```text
Room temperature is high
  ↓
System displays the information
  ↓
User decides whether to turn on the fan
```

The correct logic is:

```text
Sensor Data ≠ Automatic Command
```

A high temperature alone should not directly switch on the fan. User intent and preferences remain important.

Additional sensors like PIR or LDR can be introduced later if they add meaningful value, but they are not required for the first working version.

## Decision Flow

The backend receives information from multiple sources and routes them through validation before sending commands.

```text
Hand Gesture ─────┐
                  │
Mobile Dashboard ─┼──→ Backend / Validation ──→ MQTT ──→ ESP32
                  │
Sensor Data ──────┘
```

### Role of each source

- Gesture: expresses user intent
- Dashboard: provides direct manual control
- Sensors: provide environmental information
- Backend: validates and processes commands
- MQTT: transfers messages between components
- ESP32: controls the hardware

## Core Components

### Hardware

#### ESP32
The ESP32 acts as the primary IoT controller. It connects to Wi-Fi, reads sensor data, receives MQTT commands, and controls connected devices.

#### DHT22
Used to read temperature and humidity values.

#### Relay Module
Used to switch electrical devices safely when a relay-based control mechanism is needed.

#### LEDs / Demo Devices
Used during development and testing to demonstrate device control before connecting real appliances.

### Software

#### OpenCV
Used to access and process the camera feed.

#### MediaPipe Hands
Used to detect the user's hand and identify landmarks for gesture recognition.

#### Gesture Recognition Module
Classifies predefined hand gestures using landmark data.

#### FastAPI
Provides the backend API layer for receiving commands, validating requests, managing device states, publishing MQTT messages, and serving dashboard data.

#### MQTT
Used for communication between the backend and ESP32.

#### React
Used to build the web dashboard, allowing users to:

- View device status
- Turn devices on or off
- View temperature and humidity
- Trigger system modes
- Review recent activity

## Technology Stack

### Frontend

- React
- HTML
- CSS
- JavaScript

### Backend

- Python
- FastAPI

### Computer Vision

- OpenCV
- MediaPipe Hands

### IoT and Communication

- ESP32
- MQTT

### Sensors

- DHT22

### Optional tools

- Docker
- Git and GitHub

Docker is helpful for consistent development and deployment, but it should not block the core Version 1 build. The main priority is to get the system working reliably.

## System Flow

```text
                    USER
                   /    \
                  /      \
          Hand Gesture   Phone Dashboard
                |              |
                v              v
             Camera         Web Interface
                |              |
                v              v
        OpenCV + MediaPipe     API Request
                |              |
                +-------> Backend <-------+
                            ^
                            |
                         DHT22
                    Temperature/Humidity
                            |
                            v
                    Command Validation
                            |
                            v
                           MQTT
                            |
                            v
                          ESP32
                       /          \
                      v            v
                   Light          Fan
```

## Development Plan

### Phase 1: Basic hardware setup

- Set up the ESP32
- Connect an LED or demo device
- Test Wi-Fi connectivity
- Test MQTT communication

Goal: send a command from software and control a physical output.

### Phase 2: Backend and MQTT

- Create the FastAPI backend
- Connect the backend to the MQTT broker
- Define device control APIs
- Test command flow with the ESP32

Goal: establish a reliable backend-to-ESP32 pipeline.

### Phase 3: Hand detection

- Connect the camera
- Use OpenCV to capture frames
- Integrate MediaPipe Hands
- Display and test hand landmarks

Goal: reliably detect a hand.

### Phase 4: Gesture recognition

- Define the Version 1 gesture set
- Implement gesture detection logic
- Add confidence checks
- Connect recognized gestures to backend commands

Goal: convert gestures into reliable commands.

## Summary

This project combines computer vision, IoT, and web control into a flexible smart home interface. It supports intuitive gesture control, manual mobile control, and context-aware environment monitoring while keeping the first version realistic, reliable, and achievable within the project timeline.


Phase 5: Mobile Dashboard

Build the React interface

Display device states

Add ON/OFF controls

Add temperature and humidity display

Goal: allow manual control from a phone.

Phase 6: Sensor Integration

Connect the DHT22 to ESP32

Read temperature and humidity

Send sensor data to the backend

Display data on the dashboard

Goal: add environmental context.

Phase 7: Integration

Connect all modules:

Gesture
   +
Dashboard
   +
Sensor Data
       ↓
     Backend
       ↓
      MQTT
       ↓
     ESP32
       ↓
     Devices

Phase 8: Testing and Documentation

Test each gesture repeatedly

Test dashboard commands

Test MQTT communication

Test ESP32 reconnect behavior

Test invalid commands

Record demonstrations

Prepare project documentation

Version 1 Success Criteria

Version 1 will be considered successful when:

The camera reliably detects predefined gestures

Gestures can trigger valid commands

The dashboard can control devices manually

Commands are transmitted through MQTT

ESP32 receives and executes commands

Device states are visible on the dashboard

Temperature and humidity are displayed correctly

The complete system works on the local network

Version 2

After Version 1 is stable, Version 2 can introduce speech recognition.

The architecture will become multimodal:

Hand Gesture ──┐
               │
Speech Input ──┼──→ Intent / Backend ──→ MQTT ──→ ESP32
               │
Dashboard ─────┘

Speech recognition should be added as another input method without rebuilding the entire IoT system.

Possible Version 2 additions:

Speech recognition

Voice commands

Combined gesture and speech input

User preferences

Personalized automation

More advanced context awareness

Project Principle

The system should remain simple and reliable.

The priority for Version 1 is:

Reliable Input
    ↓
Correct Command
    ↓
Reliable Communication
    ↓
Correct Device Action

Advanced features should only be added after the basic pipeline is working properly.

Repository Structure

AI-Hand-Gesture-Smart-Home-Copilot/
│
├── frontend/              # React dashboard
│
├── backend/               # FastAPI application
│
├── gesture-recognition/   # OpenCV and MediaPipe code
│
├── esp32/                 # ESP32 firmware
│
├── docs/                  # Documentation and diagrams
│
├── roadmap.png            # Project roadmap image
│
└── README.md

Current Status

Version 1 is currently under development.

The immediate focus is to build the core pipeline:

Camera / Dashboard
        ↓
      Backend
        ↓
       MQTT
        ↓
      ESP32
        ↓
   Demo Devices

Once this pipeline is stable, gesture recognition and sensor-based context features can be integrated step by step.

Team Members

This project is developed by:

Rachit Saxena

Raghav Agarwal

Mukul Kumar

