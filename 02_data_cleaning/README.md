# 2. Data Cleaning & Preprocessing

## 📌 Objective
Clean and validate raw motorcycle telemetry data (10,350 samples at 20 Hz across 5 laps) based on the audit findings from Phase 1. The goal of this phase is to build a reproducible cleaning pipeline that removes non-informative channels, standardizes key signals, corrects sensor drift and vibration artifacts, and exports verified datasets ready for lap and telemetry analysis.

---

## 🛠️ Methodology & Steps Performed
1. Safe Working Copy: Cloned raw session data into a working DataFrame (`df_clean`) so original recordings remain untouched.
2. Channel Pruning: Dropped 26 zero-variance, inactive, or diagnostic channels flagged during validation while preserving all 10,350 time-indexed rows.
3. RPM Standardization: Resolved duplicate/scaled engine speed channels (`RPM_CHANNEL` vs. `RPM dup 2`) into a single standardized `RPM` signal.
4. Brake Pressure Calibration: Corrected negative zero-drift on `Brake_pressure` so unbraked states remain clean without false readings.
5. IMU Spike Removal & Visual Verification: Investigated high `InlineAcc` peaks against `GPS Speed`, `TPS`, and `RPM`, applied selective masking (`> 0.85 g`) with linear interpolation, left `LateralAcc` unmodified within realistic bounds, and plotted Before vs. After comparisons across the full session and a zoomed window (`720s - 745s`).
6. Final Validation & Export: Verified zero missing values (`NaN`) and final dimensions `(10,350, 41)`, then exported cleaned telemetry and lap metadata to CSV files.

---

## 📊 Key Findings & Engineering Insights
1. Signal & Channel Cleanup
- Dimensionality: Preserved all 10,350 samples at 20 Hz while reducing active columns to 41 verified channels.
- Engine Speed: Standardized `RPM` onto a single reliable signal for downstream throttle, gear, and lap analysis.
2. IMU Vibration vs. Real Acceleration
- Spike Filtering: High `InlineAcc` peaks (up to +2.840 g) occurred without matching `GPS Speed` acceleration and were confirmed as frame/engine vibration artifacts against a ~0.85 g physical limit.
- Signal Preservation: Masking values above 0.85 g followed by linear interpolation reduced `InlineAcc` maximum from 2.840 g to 0.849 g without smoothing normal braking or acceleration curves. `LateralAcc` remained within -0.459 g to 0.534 g and required no spike removal.
3. Exported Artifacts for Analysis
- Cleaned Telemetry (`Lambros_testing_0356_clean.csv`): 10,350 rows × 41 columns, 0 `NaN` values.
- Lap Metadata (`Lambros_testing_0356_laps.csv`): Lap boundaries and timing data for the 5 completed laps.
