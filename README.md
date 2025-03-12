
# 🏥 Intensive Care Unit Dashboard
**⚠️ The source code is not shared in this repository due to privacy and company policies. However, this README provides a comprehensive overview of the project, including its functionality, architecture, and techs used.**

![Dashboard Preview](https://github.com/user-attachments/assets/3c966b77-b014-430f-a88e-82d154b16d79)

---

## 📋 Overview

The Patient Vital Monitoring Dashboard is a real-time monitoring solution designed for healthcare professionals to remotely track vital signs of multiple intensive care patients simultaneously. The system connects to medical devices via MQTT protocol, parses HL7 messages, and displays critical patient data in a responsive dashboard interface.

https://github.com/user-attachments/assets/23bb6b61-97d7-44f0-a688-656d76e3b30b

---


## ✨ Features

- **Multi-patient Monitoring**: Tracks up to 6 patients simultaneously in a grid layout
- **Real-time Updates**: Visualizes vital signs with live updates via SignalR
- **Key Vital Parameters**: Monitors 5 critical vitals per patient:
  - ❤️ Heart Rate (Kalp Hızı)
  - 💓 Pulse (Nabız)
  - 🫁 Respiration Rate (Solunum)
  - 🧪 SPO2 (Oxygen Saturation)
  - 📊 PVC (Premature Ventricular Contractions)
- **Interactive UI**: Expandable patient panels with fullscreen mode
- **Dynamic Charts**: Time-series visualization with tooltips showing precise readings
- **Reference Ranges**: Displays normal parameter ranges for clinical assessment
- **Responsive Design**: Optimized layout across different screen sizes
- **Automatic Data Validation**: Handles connection loss with default values after 2 seconds

---


## 🔧 Tech Stack

### Backend
- **ASP.NET Core**: Web application framework and hosting
- **SignalR**: Real-time client-server communication
- **MQTTnet**: Client library for MQTT protocol integration
- **NHAPI**: HL7 message parsing and processing library
- **Background Services**: Continuous data processing with `IHostedService`
- **Dependency Injection**: Service container for clean architecture

### Frontend
- **JavaScript/jQuery**: Core programming and DOM manipulation
- **Highcharts**: Interactive time-series visualization
- **SignalR Client**: Real-time data reception
- **CSS Grid/Flexbox**: Responsive layout system
- **HTML5**: Modern markup for dashboard structure

---


## 🏗️ System Architecture

```
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────────────────┐
│                 │      │                 │      │                             │
│  Medical Device │──────▶   MQTT Broker   │──────▶  MqttBackgroundService     │
│  (HL7 Messages) │      │                 │      │  └─> MQTTService           │
│                 │      │                 │      │      └─> VitalDataProcessor │
└─────────────────┘      └─────────────────┘      └─────────────┬───────────────┘
                                                                │
                                                                ▼
                                                  ┌─────────────────────────────┐
                                                  │                             │
                                                  │  VitalMonitoringService     │
                                                  │  └─> SignalR Hub            │
                                                  │                             │
                                                  └─────────────┬───────────────┘
                                                                │
                                                                ▼
                                              ┌───────────────────────────────────┐
                                              │                                   │
                                              │  Browser Clients                  │
                                              │  └─> SignalR Connection          │
                                              │      └─> Highcharts Visualization │
                                              │                                   │
                                              └───────────────────────────────────┘
```

---


## 🔄 Core Components

### Data Flow
1. **Acquisition**: Medical devices transmit HL7 messages to MQTT broker
2. **Processing**:
   - `MQTTService` connects to broker and subscribes to topics
   - `VitalDataProcessor` parses messages using NHAPI (HL7 parsing library)
   - HL7 v2.x messages are decoded and vital parameters extracted from OBX segments
   - Data is validated and stored with timestamps
3. **Distribution**:
   - `VitalMonitoringService` monitors for updates
   - New data is formatted and sent via SignalR's `ReceiveData` method
   - Patient data is rotated among 6 display slots for demonstration
4. **Visualization**:
   - Frontend receives data through SignalR connection
   - Highcharts updates in real-time with smooth animations
   - UI reflects the latest values with appropriate units

### Key Classes

#### 📊 `VitalData.cs`
Core model storing vital signs with:
- Value properties: `KalpHizi`, `Nabiz`, `SolunumHizi`, `SPO2`, `PVC`
- Unit properties: `BirimKalpHizi`, `BirimNabiz`, etc.
- Reference ranges: `KalpHiziRef`, `SolunumHiziRef`, etc.

#### 🔍 `VitalDataProcessor.cs`
Handles HL7 message parsing (using NHAPI):
- `ParseAndAddVitalData()`: Entry point for new messages
- `AnalyzeHL7Message()`: Extracts vitals from OBX segments using NHAPI for HL7 structure parsing
- Implements timeout logic to reset values after 2 seconds of inactivity
- Processes standard HL7 v2.x message format for healthcare data exchange

#### 📡 `MQTTService.cs`
Manages MQTT connectivity:
- Connects to broker and subscribes to HL7 topics
- Processes incoming messages and forwards to data processor
- Handles connection errors and reconnection

#### 🔄 `VitalMonitoringService.cs`
Orchestrates data distribution:
- Implements `IHostedService` for background processing
- Subscribes to `DataUpdated` events from processor
- Formats data for frontend consumption
- Distributes to all clients via SignalR hub

#### 📲 `MonitorHub.cs`
SignalR hub for real-time communication:
- Minimal implementation leveraging `IHubContext` for server-initiated messages
- Used by `VitalMonitoringService` to broadcast updates

### Frontend Implementation

#### 🖼️ UI Layout
- CSS Grid layout with 6 equal cells (2×3)
- Each cell contains a patient panel with:
  - Header with patient name and fullscreen toggle
  - 5 stacked charts in a flexbox column

#### 📈 Visualization
- Highcharts time-series charts with custom styling
- Color coding:
  - ❤️ Heart Rate (red)
  - 🌿 SPO2 (green)
  - 🔵 Respiration (blue)
  - 🟡 PVC (yellow)
  - 💓 Pulse (purple)
- Dynamic updates with smooth transitions
- Custom tooltips showing value, timestamp, and units

#### ⚡ Real-time Updates
- SignalR connection maintained via JavaScript client
- New data processed and distributed to appropriate patient panels
- Chart data limited to 20 points with FIFO behavior

---

## 🔮 Future Enhancements

- **🚨 Alarm System**: Configurable alerts for out-of-range values
- **🔐 Authentication**: Secure access for authorized healthcare personnel
- **🛏️ Patient Assignment**: Dynamic patient selection and bed assignment

