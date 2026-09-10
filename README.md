# Antarctic Mirror 🌐❄️

**A Digital Twin Framework for Efficient Remote Management of Maitri & Bharati Research Stations**

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100%2B-009688)](https://fastapi.tiangolo.com)
[![MQTT](https://img.shields.io/badge/MQTT-paho--mqtt-660066)](https://mqtt.org)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-1.3%2B-F7931E)](https://scikit-learn.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Antarctic Mirror is an **edge-native Digital Twin** platform that creates a live, continuously updated virtual replica of India's Maitri and Bharati research stations. It layers machine learning and optimization on top of this replica to forecast problems before they occur, recommend the cheapest safe action, and keep operating fully during satellite blackouts.

The system addresses the unique operational constraints of Antarctic stations: **seasonal resupply windows** (Nov–Feb only), **intermittent 56 Kbit/s satellite links**, **6-month unmanned winter periods**, and **diesel-dependent power systems**.

---

## 📋 Table of Contents

- [Problem Statement](#-problem-statement)
- [Solution Overview](#-solution-overview)
- [Key Features](#-key-features)
  - [Four ML Pillars](#four-ml-pillars)
  - [Station Copilot – Signature AI Feature](#station-copilot--signature-ai-feature)
  - [Edge-Native Architecture](#edge-native-architecture)
- [System Architecture](#-system-architecture)
- [Tech Stack](#-tech-stack)
- [Data Sources](#-data-sources)
- [Performance Metrics](#-performance-metrics)
- [Getting Started](#-getting-started)
- [Project Structure](#-project-structure)
- [Future Work](#-future-work)
- [Contributing](#-contributing)
- [License](#-license)
- [Acknowledgments](#-acknowledgments)

---

## 🧊 Problem Statement

India operates two permanent research stations in Antarctica under the Indian Antarctic Programme:

| Station | Established | Location | Crew (Winter/Summer) | Power |
|---------|-------------|----------|----------------------|-------|
| **Maitri** | 1989 | Schirmacher Oasis (70.77°S, 11.73°E) | 25 / 40–45 | 4 × 62.5 KVA diesel gensets |
| **Bharati** | 2012 | Larsemann Hills (69.40°S, 76.19°E) | 20–47 / up to 72 | Diesel + renewables |

**Operational constraints:**
- **Seasonal resupply only**: Air operations (Ilyushin-76) run only in summer (Nov–Feb/Mar). No direct flights to Bharati. A stockout outside this window can mean **months of exposure**.
- **Intermittent, low-bandwidth satellite communication**: Typically **56 Kbit/s** with high latency.
- **6-month unmanned winter period**: Small crews cannot call for outside help.
- **Manual management**: Logs, radio check-ins, and operator experience drive decisions.

**The need:** A remote management tool that predicts failures, optimizes resource use, and keeps working **offline**.

---

## 💡 Solution Overview

**Antarctic Mirror** is a **genuine Digital Twin**—not just a dashboard. It satisfies all three tests:

1. **State replication** – Every asset (generators, battery, buildings, fuel tanks) is a modelled node with live-updating properties.
2. **Predictive simulation** – Runs forward in time (load forecasts, strain projections, inventory depletion).
3. **What-if capability** – Users can change inputs (e.g., "Generator 1 fails") and see downstream effects across all systems.

**Core principle:** All critical intelligence runs **at the edge** (locally on-station). The cloud layer exists for situational awareness and long-term analytics—not for control.

---

## 🔑 Key Features

### Four ML Pillars

| # | Pillar | Core Question | Techniques |
|---|--------|---------------|------------|
| 1 | **Energy** | Will we have enough power in 24h? What's the cheapest safe generator schedule? | Gradient-boosted load forecasting (<10% MAPE); MILP dispatch optimization (8%+ fuel savings) |
| 2 | **Infrastructure** | Any early signs of structural or equipment failure? | Outlier detection (~100% precision, >90% recall); simplified beam-stress physics for strain monitoring |
| 3 | **Logistics** | Will we run out of fuel/food/spares before resupply? | SARIMA demand forecasting; reorder-point optimization with safety stock tied to real seasonal windows |
| 4 | **Environment** | What's the weather doing, and how does it change other risks? | Real NCPOR AWS data ingestion; first-order thermal models; risk modulation across pillars |

### Station Copilot – Signature AI Feature

An LLM-powered conversational assistant that fuses outputs from all four pillars and answers plain-English questions:

> **User:** "Anything I should worry about today?"  
> **Copilot:** "Yes – two things. Generator 1's vibration sensor is reading 3.2× above its normal baseline, flagged 12 minutes ago; I'd inspect it before the next shift. Also, fuel is projected to cross the reorder threshold in 9 days, and the next resupply flight window doesn't open for 6 weeks, so I'd recommend placing the order today rather than waiting."

**Offline fallback:** During satellite outages, the Copilot switches to a rule-based mode using cached state, clearly labelled with a last-synced timestamp.

### Edge-Native Architecture

- **Edge Sensing:** Power meters, vibration sensors, strain gauges, AWS instrumentation → local gateway (NVIDIA Jetson-class).
- **Edge Intelligence:** All four ML pillars execute locally. Every reading written to a persistent, priority-tagged buffer.
- **Adaptive Synchronization:** Store-and-forward MQTT with priority tiers (critical → high → medium → low). Batch size adapts to available bandwidth.
- **Cloud Services:** Message broker, time-series data lake, REST API, LLM API integration.

**Result:** 0% data loss during blackouts; >85% payload compression; full functionality offline.

---

## 🏗 System Architecture
