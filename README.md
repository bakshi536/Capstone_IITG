# Capstone_IITG
# 🚗 Dynamic Pricing for Urban Parking Lots

This project simulates **real-time dynamic pricing** for parking lots based on demand signals using historical parking data. It aims to optimize prices dynamically to manage occupancy, congestion, and demand elasticity in urban environments.

---

## 📌 Overview

Urban parking suffers from static pricing models that don’t adapt to varying demand throughout the day. This project introduces a **real-time dynamic pricing system** that factors in:

- Occupancy rate
- Queue length
- Traffic density
- Vehicle arrival rate
- Special day indicators (e.g., weekends/holidays)

By implementing a normalized demand-based pricing model, we aim to:
- Increase pricing fairness
- Improve lot utilization
- Reduce traffic from cruising vehicles

---

## 🧠 Tech Stack

| Layer        | Tool/Library                    |
|--------------|---------------------------------|
| Data Stream  | Pathway                         |
| Visualization| Matplotlib, Seaborn, Plotly     |
| Notebook     | Google Colab                    |
| Storage      | JSONL, CSV                      |
| Scripting    | Python (NumPy, Pandas)          |
| Versioning   | Git + GitHub                    |

---

## 🧱 Project Architecture & Workflow
This project simulates real-time dynamic pricing for urban parking lots using streaming data and demand-based modeling. Below is a breakdown of each component in the architecture:

1. 📥 Data Ingestion
Source: Historical parking transaction data (CSV format), containing:
Timestamp, LotID, OccupancyRate, Queue, Traffic, IsSpecialDay, etc.

->Preprocessing:
-Timestamp is cleaned and converted to Python datetime.
-Features such as day and hour are extracted for aggregation.
-Data is sorted by LotID and Timestamp.

2. ⚙️ Real-Time Stream Processing using Pathway
A streaming engine is built using Pathway, which:
-Ingests preprocessed data into a dynamic pipeline.
-Applies a 1-hour tumbling window to simulate real-time updates.
-Groups by LotID to process each parking lot independently.

3. 📊 Feature Aggregation (Hourly Windows)
Each 1-hour window aggregates key features:

->Feature	Aggregation

-NormOccupancy	Mean

-NormQueue	Mean

-NormTraffic	Mean

-NormVehicle	Mean

-IsSpecialDay	Max

These averages are the basis for computing the demand score.

5. 📈 Model 2: Demand Calculation
A linear demand model is applied per lot per hour:

demand = a * occ_avg + b * queue_avg - c * traffic_avg + d * special_any + e * vehicle_avg

Constants a, b, c, d, e are hyperparameters from prior tuning.

The goal is to capture demand dynamics per lot in real time.

5. 🔁 Normalization & Price Mapping
To ensure pricing is consistent across lots:

->Normalization is applied:

norm_demand = (demand - demand_min) / (demand_max - demand_min)

->Dynamic Price is computed using:

price = BASE_PRICE * (1 + λ * norm_demand)

->Price is clipped to stay within bounds:

min_price = BASE_PRICE * 0.5

max_price = BASE_PRICE * 2.0

6. 📤 Output Streaming
The final prices are streamed to a .jsonl file in the format:
e.g
{
  "t": "2025-07-07 10:00:00",
  "LotID": "BHMCCCTHL01",
  "DemandPriceLive": 13.27
}
This file powers live dashboards, alerts, or pricing APIs.

8. 📊 Visualization
Visual outputs include:

-Real-time line plots of DemandPriceLive vs. time per lot

-Occupancy vs. Price scatter plots to assess responsiveness

-Lambda sensitivity curves showing smoothness vs. reactivity

->These help stakeholders understand:

-How pricing reacts to live demand

-Which parameter setups perform best

-Model behavior over time

---
