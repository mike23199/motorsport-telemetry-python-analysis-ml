# 01. Exploratory Data Analysis (EDA) & Data Validation

## 📌 Objective
Perform an initial technical audit of raw motorcycle telemetry data recorded during track testing sessions. The goal of this phase is to verify data integrity, validate sampling continuity, map sensor units, and identify hardware anomalies or signal noise prior to data cleaning.

---

## 🛠️ Methodology & Steps Performed
1. **Metadata & Context Inspection:** Extracted session configuration details (ECU type, logging frequency, and sensor calibration ranges).
2. **Schema & Data Dictionary:** Inspected dataset dimensions (`10,350` samples × `71` channels) and mapped each telemetry channel to its physical measurement unit.
3. **Lap Overview:** Verified completed timed laps (`5` laps) and identified the fastest lap (`Lap 5: 1:39.736`) using numeric lap duration values.
4. **Automated Sensor Audit:** Checked for missing values (`NaN`) and ran automated zero-variance validation to isolate inactive or diagnostic channels.
5. **Time-Series Continuity & Sanity Checks:** Verified exact `20 Hz` sampling consistency (`dt = 0.0500s`) and evaluated `min`, `mean`, and `max` bounds across engine, rider-input, suspension, and IMU channels.
6. **Visual Sanity Check:** Plotted critical health channels (`ECT`, `BATTERY`, `InlineAcc`) across the entire session timeline to distinguish gradual physical trends from high-frequency vibration spikes.

---

## 📊 Key Findings & Engineering Insights

### 1. Data Integrity & Continuity
* **Completeness:** `0` missing values (`NaN`) and `0` duplicate timestamps across all `10,350` rows.
* **Sampling Stability:** Strictly consistent `20 Hz` (`dt = 0.0500s`) logging interval with zero dropouts.

### 2. Sensor Health & Channel Audit
* **Zero-Variance Channels (`26` flagged):** Disconnected sensors (`AFR`, `IAT`), inactive default `RPM`, static ECU settings (`MAP_SEL = 3`, `TC_MAP_LEVEL = 0`), and internal diagnostic flags carry constant values and are marked for removal.
* **Engine Speed (`RPM`) Scaling:** Active engine speed is split between `RPM_CHANNEL` (max `14,762 rpm`) and `RPM dup 2` (max `13,685 rpm`), differing by a constant `~1.078x` pulse-per-revolution scaling factor.
* **Signal Offsets & Noise:**
  * `Brake_pressure` (calibrated `0–100%` of sensor full scale) shows minor negative zero-drift (`min = -0.72%`) when unapplied.
  * `InlineAcc` (IMU) exhibits isolated positive spikes up to `+2.84 g` caused by chassis vibrations.
  * `Front_Travel` (`0–106 mm` calibration) reaches `-7.51 mm` during full fork extension due to static-sag zeroing.

### 3. Trackside Hardware Alerts
* **Battery Voltage (`BATTERY`):** Dropped gradually to `10.14 V` during Laps 1–4 before recovering to `~12.1 V` at the start of Lap 5 — flagged for terminal and charging system inspection.
* **Coolant Temperature (`ECT`):** Smooth warmup from `49.1°C` to `62.0°C`, indicating the engine ran slightly below optimal race temperature (`75–85°C`).

---
*Note: Raw `.xrk` telemetry logs and internal parsing wrappers are excluded from this repository to protect team proprietary data.*
