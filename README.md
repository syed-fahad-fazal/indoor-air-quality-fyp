# 🌬️ Indoor Air Quality Monitoring, Analysis & Decision Support System

> Final Year Project — Hamdard University, Karachi (2023)
> IoT-based multi-environment air quality monitoring system with cloud integration, AQI calculation, and Power BI data visualization across 4 real-world scenarios.

---

## 👥 Project Team

| Role | Name |
|---|---|
| Developer & Data Analyst | **Qazi Syed Fahad Ahmed** |
| Hardware Engineer | Anzal Iftikhar |
| Supervisor | Dr. Tayyab Ahmed Shaikh |

---

## 🎯 Problem Statement

Indoor Air Quality (IAQ) is a critical but often overlooked health factor. Poor IAQ leads to respiratory issues, fatigue, and long-term illness. Existing systems lacked:
- Multi-environment comparative analysis
- Real-time cloud-based monitoring
- Actionable AQI-based decision support
- Data visualization for non-technical users

This project bridges that gap with an end-to-end IoT + Cloud + Power BI solution.

---

## 🏗️ System Architecture

```
[Gas Sensors x6]
MQ3 · MQ4 · MQ7 · MQ8 · MQ9 · MQ135
        ↓
[Arduino Uno] — Reads analog sensor inputs
        ↓ (UART Protocol)
[NodeMCU ESP8266] — Wi-Fi Gateway
        ↓
[ThingSpeak Cloud] — Real-time storage & remote access
        ↓
[Excel] — AQI calculation & data structuring
        ↓
[Power BI] — Advanced visualization & decision support
```

---

## 📡 Hardware Components

| Component | Role |
|---|---|
| Arduino Uno | Central processing — reads all 6 sensors via analog pins |
| NodeMCU ESP8266 | Wi-Fi module — transmits data to ThingSpeak cloud |
| MQ3 Sensor (A0) | Alcohol, LPG, methane, propane, hydrogen, smoke |
| MQ4 Sensor (A1) | LPG, methane, propane, natural gas |
| MQ7 Sensor (A2) | Carbon monoxide (CO) detection |
| MQ8 Sensor (A3) | Hydrogen (H2) detection |
| MQ9 Sensor (A4) | CO, LPG, methane, propane |
| MQ135 Sensor (A5) | NH3, NO2, SO2, CO, formaldehyde — general AQI |
| CoolTerm Software | Offline serial data logging to CSV |

**Wiring:** All sensors connected to Arduino analog pins A0–A5. NodeMCU connected via TX/RX pins using UART protocol. Common GND and VCC shared across all sensors.

---

## 🌍 4 Test Scenarios — 8 Hours Each

This is the core of the project — data collected across 4 distinct real-world environments:

### Scenario 1 — Normal Room (Window Open)
- Baseline indoor environment with natural ventilation
- Measured typical residential air quality
- Lower pollutant levels due to fresh air exchange
- **AQI Result:** Good to Moderate range

### Scenario 2 — Kitchen Environment
- High CO and CO2 levels during cooking activities
- MQ7 (CO) and MQ135 (general AQ) showed significant spikes
- Ventilation impact clearly visible in time-series data
- **AQI Result:** Unhealthy for Sensitive Groups during cooking peaks

### Scenario 3 — Air-Conditioned Room (Closed)
- Closed environment with recirculated air
- CO2 buildup observed over time with no fresh air
- MQ4 and MQ9 readings elevated due to sealed environment
- **AQI Result:** Moderate — worsening over time without ventilation

### Scenario 4 — Industrial Textile Environment ⚠️ WORST AQI
- Highest pollutant readings across ALL sensors
- Textile manufacturing releases volatile organic compounds (VOCs), dust, and chemical fumes
- MQ135 (NH3, SO2, formaldehyde) readings critically elevated
- Multiple sensors breached hazardous thresholds simultaneously
- **AQI Result:** Hazardous — highest AQI recorded across all 4 scenarios
- **Insight:** Textile workers face serious long-term health risks without proper ventilation and PPE

---

## 📊 Power BI Dashboard — Data Analysis

Power BI was used for advanced visualization and decision support across all 4 scenarios.

### Dashboards Built:
- **Comparative AQI Dashboard** — Side-by-side AQI comparison across all 4 environments
- **Time-Series Analysis** — 8-hour pollutant trend lines per sensor per scenario
- **Sensor-wise Breakdown** — Individual gas readings (PPM/PPB/mg/L) per environment
- **AQI Index Gauge** — Real-time AQI category visualization (Good → Hazardous)
- **Worst Scenario Highlight** — Textile environment flagged with critical threshold alerts
- **Decision Support Panel** — Actionable recommendations based on AQI levels

### DAX Measures Used:
```dax
-- AQI Category Classification
AQI Category =
SWITCH(
    TRUE(),
    [AQI Value] <= 50, "Good",
    [AQI Value] <= 100, "Moderate",
    [AQI Value] <= 150, "Unhealthy for Sensitive Groups",
    [AQI Value] <= 200, "Unhealthy",
    [AQI Value] <= 300, "Very Unhealthy",
    "Hazardous"
)

-- Average pollutant per scenario
Avg CO Level = AVERAGEX(FILTER(SensorData, SensorData[Scenario] = "Kitchen"), SensorData[MQ7_CO_PPM])

-- Peak reading detection
Peak AQI = MAXX(SensorData, SensorData[AQI_Value])
```

---

## ☁️ Software Stack

| Tool | Purpose |
|---|---|
| Arduino IDE (C) | Sensor reading & data collection code |
| CoolTerm | Serial data logging to CSV (offline) |
| NodeMCU Firmware | Wi-Fi transmission to ThingSpeak |
| ThingSpeak | Cloud-based real-time data storage |
| Microsoft Excel | AQI formula calculations, data structuring |
| Power BI Desktop | Advanced visualization & decision dashboards |

---

## 🧮 AQI Calculation Methodology

Sensor voltage readings converted to gas concentration units:
- **PPM** (Parts Per Million) — MQ3, MQ4, MQ7, MQ9
- **PPB** (Parts Per Billion) — MQ8
- **mg/L** (Milligrams per Liter) — MQ135

AQI calculated using EPA standard formula applied in Excel:

```
AQI = ((AQI_high - AQI_low) / (C_high - C_low)) × (C - C_low) + AQI_low
```

Where C = measured concentration, breakpoints from EPA IAQ standards.

---

## 🔑 Key Findings

| Environment | Worst Pollutant | AQI Category | Health Risk |
|---|---|---|---|
| Normal Room (Window Open) | Low across all | Good | Minimal |
| Kitchen | CO (MQ7 spike) | Unhealthy (during cooking) | Short-term exposure risk |
| AC Room (Closed) | CO2 buildup | Moderate → Unhealthy | Worsens with time |
| **Textile Industrial** | **VOCs + NH3 + SO2** | **Hazardous** | **Critical long-term risk** |

**Key Conclusion:** The textile industrial environment recorded the worst AQI across all scenarios — driven by chemical fumes, VOCs, and particulate matter from manufacturing processes. Immediate ventilation improvement and PPE are critical for worker safety.

---

## 📁 Repository Structure

```
indoor-air-quality-fyp/
├── hardware/
│   ├── circuit-diagram.png       — Arduino + NodeMCU wiring
│   ├── sensor-pinout.md          — Sensor to pin mapping
│   └── component-list.md
├── code/
│   ├── arduino-sensor-read.ino   — Main Arduino data collection code
│   ├── nodemcu-thingspeak.ino    — NodeMCU cloud transmission code
│   └── pseudocode.md             — Annotated pseudocode
├── data/
│   ├── sample-readings.csv       — Anonymized sensor readings sample
│   └── aqi-calculations.xlsx     — AQI formula workbook
├── powerbi/
│   ├── screenshots/              — Dashboard screenshots
│   └── dax-measures.md           — All DAX formulas used
├── presentation/
│   └── fyp-presentation.pptx     — Original FYP presentation
├── docs/
│   ├── abstract.md
│   ├── methodology.md
│   └── references.md
└── README.md
```

---

## 🔮 Future Work

- Mobile app for real-time remote monitoring
- AI/ML model for AQI prediction from historical patterns
- Real-time SMS/push alerts when AQI crosses thresholds
- Automated IoT actuators (fans, purifiers) triggered by AQI
- Expanded to outdoor environments for comparative analysis

---

## 📚 References

1. Kim et al. (2020) — Sensor-based wireless IAQ monitoring. *Journal of Ambient Intelligence and Humanized Computing*
2. Pandey et al. (2021) — IoT-based IAQ monitoring system. *Measurement, 174*
3. Zhang et al. (2021) — Cloud-based IAQ with ML diagnosis. *Sensors, 21(1)*
4. Niazi et al. (2020) — Smart IAQ monitoring with ML for VOC detection. *Journal of Intelligent & Fuzzy Systems*

---

*Final Year Project — BE Computer System Engineering*
*Hamdard University, Karachi | 2023*
*Built by [Syed Fahad Fazal](https://linkedin.com/in/syed-fahad-fazal)*
