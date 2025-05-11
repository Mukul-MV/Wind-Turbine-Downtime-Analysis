# Wind Turbine Power Production & Downtime Analysis

## Table of Contents

1. [Overview](#overview)
2. [Project Agenda](#project-agenda)
3. [Dataset Description](#dataset-description)
4. [Data Preprocessing](#data-preprocessing)
5. [Interactive Dashboard](#interactive-dashboard)
6. [Exploratory Data Analysis](#exploratory-data-analysis)

   * Descriptive Statistics
   * Correlation Analysis
   * Power vs. Wind Speed Relationship
7. [Downtime Analysis](#downtime-analysis)

   * Turbine Down Periods
   * Root Causes
   * Energy Loss Estimation
8. [Key Inferences](#key-inferences)
9. [Future Work](#future-work)

---

## Overview

This repository contains a comprehensive analysis of wind turbine power generation, from raw data ingestion to interactive dashboards and key insights. The core objectives are:

* Compare **actual** vs. **expected** power output.
* Analyze the effect of **wind speed** on generation.
* Identify and quantify **downtimes** and potential causes.
* Visualize results through a Power BI dashboard.

> ![image](https://github.com/user-attachments/assets/1bf3bcd2-9fc0-490e-b794-35c7a07957a6)

---

## Project Agenda

1. **Data Ingestion**: Load raw time-series turbine data.
2. **Preprocessing**: Clean timestamps, status flags, and engineer new features.
3. **Dashboard Development**: Design interactive Power BI visuals.
4. **Exploratory Analysis**: Statistical summaries and correlations.
5. **Downtime Analysis**: Quantify downtime events and energy loss.
6. **Insights & Recommendations**: Summarize findings and propose next steps.

---

## Dataset Description

The dataset includes:

| Column             | Description                                |
| ------------------ | ------------------------------------------ |
| `timestamp`        | Date and time of reading                   |
| `nacelle_wind_speed` | Wind speed at nacelle (m/s)                |
| `actual_power`     | Measured power output (kW)                 |
| `expected_power`   | Theoretical power based on wind speed (kW) |
| `turbine_status`   | Operational state (100 = up, 0 = down)       |

---

## Data Preprocessing

* **Timestamp Standardization**: Converted `timestamp` to `datetime`, extracted day/month.
* **Missing Values**: Removed incomplete records.
* **Feature Engineering**:

  * **Power Efficiency**: `(actual_power / expected_power) * 100`.
  * **Downtime Flag**: Turbine off when status = 0 or wind < cut‑in.

---

## Interactive Dashboard

The Power BI dashboard contains:

* **Scatter Plot** (Wind speed vs. Actual power)

   ![image](https://github.com/user-attachments/assets/a90a617e-1af9-4109-ad90-c7fe03f7be7d)

* **Day-wise Bar Chart** (Actual vs. Expected power)

   ![image](https://github.com/user-attachments/assets/95ee7b05-6dd5-4df5-8583-e328ef4483fd)

* **Line Chart** (Average wind speed trend)

   ![image](https://github.com/user-attachments/assets/6f97b486-4239-42b8-864f-a50884547485)

* **Monthly Area Chart** (Actual vs. Expected by month)

   ![image](https://github.com/user-attachments/assets/1fed4467-b6df-4a9d-94d2-077a7d02325e)

* **Days vs. Working Days** combo chart

   ![image](https://github.com/user-attachments/assets/df9b2cbe-d0ff-48a7-b2ff-63b08663ecc8)


---

## Exploratory Data Analysis

### Descriptive Statistics

### 📊 Wind Turbine Dataset – Inference Summary

#### 🌬️ Wind Profile via LIDAR Readings

| Height     | Mean Wind Speed (m/s) | Std. Dev. (m/s) |
|------------|-----------------------:|----------------:|
| Lidar_60m  |                  5.57  |           2.10  |
| Lidar_140m |                  6.28  |           2.66  |
| Lidar_200m |                  6.79  |           3.11  |

> *Wind speed naturally increases with altitude due to lower surface friction; variability also grows with height.*

---

#### ⚡ Turbine Power Output

| Metric               | Actual Power (kW) | Expected Power (kW) |
|----------------------|-------------------:|---------------------:|
| **Mean**             |          1,022     |             1,246    |
| **Median**           |            604     |               864    |
| **Max**              |          3,352     |             3,300    |
| **Min**              |            –74     |               0      |

> *On average, Actual < Expected, indicating underperformance. Negative actual values correspond to idle-state consumption.*

---

#### 🔋 Energy Generation (kWh)

| Metric               | Actual Generation | Expected Generation |
|----------------------|------------------:|--------------------:|
| **Mean**             |            170.4  |              207.7  |
| **Median**           |            100.7  |              143.9* |
| **Max**              |            558.7  |              550.0* |

> *Actual generation frequently falls short of expected values—signaling efficiency losses.*  
> *Expected generation per record computed from `Expected_Power` × Δt.

---

#### 🏗️ Turbine Operational Status

| Metric | Value (%) |
|--------|----------:|
| **Mean**   |   94.11 |
| **Max**    |  100.00 |
| **Min**    |    1.00 |

> *The turbine is available ~95% of the time, with only minor downtimes.*

---

#### 🌪️ Wind Speed at Hub Height

| Metric             | Value (m/s) |
|--------------------|------------:|
| **Mean**           |        5.93 |
| **Median**         |        5.72 |
| **75th Percentile**|        7.98 |

> *Wind conditions are generally favorable for generation.*

---

#### ✅ Overall Insights

- ⚠️ **Underperformance:** Actual power and energy generation consistently lower than expected—investigate mechanical or calibration issues.  
- 🌬️ **Height Effect:** LIDAR confirms higher and more variable winds at greater elevations.  
- 🔄 **High Availability:** Turbine runs ~95% of the time—downtimes are minimal but impactful.  
- 📉 **Data Quality:** Filter or correct negative/zero values in power and generation before modeling.  

---

## Downtime Analysis

#### In Downtime Analysis, we expected to identify when and why the wind turbine was not generating power, despite wind being available, by analyzing logs/data. The goal is to:

🎯 Find out:
## 1) When the turbine was down.
   ![image](https://github.com/user-attachments/assets/ba4f0705-8936-4bb9-8969-b9c274cc39a3)
   
## 2) Why Was the Turbine Down?

##### a) **Non-operational Turbine (`Status ≠ 100`)**
- In this situation, lower or no power generation is expected and not a cause for immediate concern.
- However, if the turbine remains non-operational for **extended periods (multiple hours or days)**, it should be investigated.

##### b) **Wind Speed Below Cut-in Speed**
- When wind speed is below the cut-in threshold, less or no power generation is expected.
- Again, while this is normal, a **prolonged duration** of low wind speed should be noted and monitored.

##### c) **Wind Speed Above Cut-in Speed, but Power is Low**
- This situation requires closer attention.

  - If the turbine is **non-operational** (`Status <= 100`):  
    → Power drop is expected but **should not persist** for long durations.

  - If the turbine is **operational** (`Status = 100`):  
    → This is **a major concern**, as power should be generated. Possible causes include:
    - Sensor or communication errors  
    - Nacelle misalignment or yaw system error  
    - SCADA-controlled shutdown due to safety limits  
    - Electrical failures  
    - Other internal faults — in such cases, it's best to contact the **turbine vendor** for a detailed inspection or diagnostics.

## 3) How long it was down.

- Wind Speed Condition Summary (Per Month) when power generation is less than 100%

| Wind Speed Condition | Month         | Count | Total Hours | Total Days |
|----------------------|---------------|-------|--------------|-------------|
| Above Cut-in         | January (1)   | 2694  | 449.00       | 18.71       |
| Below Cut-in         | January (1)   | 652   | 108.67       | 4.53        |
| Above Cut-in         | February (2)  | 1740  | 290.00       | 12.08       |
| Below Cut-in         | February (2)  | 439   | 73.17        | 3.05        |
| Above Cut-in         | March (3)     | 166   | 27.67        | 1.15        |
| Below Cut-in         | March (3)     | 16    | 2.67         | 0.11        |
| Above Cut-in         | December (12) | 1918  | 319.67       | 13.32       |
| Below Cut-in         | December (12) | 333   | 55.50        | 2.31        |

  - **January 2025** (Total Days = 31)
      - **Above Cut-in**: 18.70 days
      - **Below Cut-in**: 4.52 days
  - **February 2025** (Total Days = 28)
      - **Above Cut-in**: 12.08 days
      - **Below Cut-in**: 3.04 days
  - **March 2025** (Total Days = 3)
     - **Above Cut-in**: 1.15 days
     - **Below Cut-in**: 0.11 days  
  -  **December 2024** (Total Days = 26)
     -  **Above Cut-in**: 13.31 days
     -  **Below Cut-in**: 2.31 days  


 - 📌 Key Takeaways
     - Wind was **mostly above cut-in speed** across all months.
     - **January and February** show strong consistency in usable wind days.
     - The **few below cut-in days** are normal and expected, but should be watched if they increase over time.

#### Turbine Status Summary (Per Month) 
| Turbine Status     | Month         | Count | Total Hours | Total Days |
|--------------------|---------------|-------|--------------|-------------|
| Non Operational    | January (1)   | 903   | 150.50       | 6.27        |
| Operational        | January (1)   | 1791  | 298.50       | 12.44       |
| Non Operational    | February (2)  | 231   | 38.50        | 1.60        |
| Operational        | February (2)  | 1509  | 251.50       | 10.48       |
| Non Operational    | March (3)     | 16    | 2.67         | 0.11        |
| Operational        | March (3)     | 150   | 25.00        | 1.04        |
| Non Operational    | December (12) | 481   | 80.17        | 3.34        |
| Operational        | December (12) | 1437  | 239.50       | 9.98        |

##### Where Wind speed > cut-in and Power Genration < 100% ( including zero)

- **January 2025** (Total Days = 31)  
    - **Operational**: 12.44 days  
    - **Non Operational**: 6.27 days  

- **February 2025** (Total Days = 28)  
    - **Operational**: 10.48 days  
    - **Non Operational**: 1.60 days  

- **March 2025** (Total Days = 3)  
    - **Operational**: 1.04 days  
    - **Non Operational**: 0.11 days  

- **December 2024** (Total Days = 26)  
    - **Operational**: 9.98 days  
    - **Non Operational**: 3.34 days  

##### 📌 Key Takeaways
- **January and February** show high operational time, indicating downtime and need to investigate why during operational we have power loss?

## 4) How much power or energy was lost during that period.
**Now we are concern when wind is above cut-in and turbine is in operational state and we are getting less power generation**
| Year | Month | Actual_Power | Expected_Power | Power_Achievement_Ratio_% | Power_lost_% |
|------|-------|--------------|----------------|---------------------------|--------------|
| 2024 | 12    | 2,252,485.10 | 2,634,838.20  | 85.49                     | 14.51        |
| 2025 | 1     | 1,535,916.55 | 2,797,021.20  | 54.91                     | 45.09        |
| 2025 | 2     | 1,857,862.91 | 2,512,028.20  | 73.96                     | 26.04        |
| 2025 | 3     | 248,302.33   | 290,840.00     | 85.37                     | 14.63        |

#### 📌 Key Inferences

- **High performance** observed in **December 2024 (85.49%)** — very close to expected power output.
- **Major performance drop** in **January 2025**, with only **54.91%** power achieved and **45.09% lost**,
    - indicating possible issues like turbine downtime, poor wind conditions, or maintenance outages.
- **February 2025** shows **moderate loss** (26.04%) — better than January but still not optimal.


![image](https://github.com/user-attachments/assets/896bd448-65c7-48de-be97-1d6652f1ad7c)

![image](https://github.com/user-attachments/assets/8f4028ff-549b-4d2e-9fd6-9469215cf2fd)

![image](https://github.com/user-attachments/assets/a6518757-5559-4caa-bf68-aaaf74acc332)

**Figure (1):**

##### This graph is when wind speed more than cutin speed , turbine is operational
- We can see that december is ideal month where mostly power loss is very less
- janurary has more power losses , continuously throughout the month


![image](https://github.com/user-attachments/assets/7390133f-2797-4a95-bd9c-36c304b2344c)

**Figure (2):** Scatter plot comparing Wind Speed vs Actual and Expected Power for selected months. Most actual power values (blue) follow the expected curve (orange), but noticeable deviations—particularly below the expected curve—indicate performance inefficiencies or downtime even at favorable wind speeds.


![image](https://github.com/user-attachments/assets/241a20ed-f3a0-4082-b289-decc8da89c4b)

**Figure (3):** Scatter plots of Wind Speed vs Power (Actual vs Expected) for selected months (under operational conditions only).
Each plot shows power output when wind speed was above cut-in threshold and the turbine was operational. The orange curve represents expected power at given wind speeds, while the blue dots represent actual power. Points significantly below the expected curve highlight underperformance despite operational status.

- **Deviation Magnitude Varies by Month:**  
  The spread of blue dots below the orange curve is wider in Months Jan and Feb compared to Month Dec, suggesting greater inefficiencies or losses during those periods.

- **Closer Alignment at High Speeds:**  
  At higher wind speeds (above ~10 m/s), actual and expected power tend to align more closely, possibly due to reduced influence of environmental or mechanical variability.
  
---

# 🛠️ Problem Statement
**The wind turbine is working most of the time (about 95% uptime), and the wind speed is good, especially at higher altitudes as seen from the LIDAR readings. However, the actual power and energy generated are often lower than what we expect.Since the turbine is running and getting enough wind, we are not sure why there is a loss in power. So, we reached out to the vendor to understand why the turbine is underperforming even in good conditions.**

## 🧾 Vendor Feedback

The vendor suspects the power loss may be due to the presence of **negative wind shear**, where wind speed **decreases with height**. This condition can result in **non-uniform loading across the rotor**, reducing overall turbine efficiency.

The vendor recommends verifying that the **wind shear exponent (α)** is within the optimal range of **0.0 to 0.3**, where the turbine performance is typically maximized. Values outside this range—especially **negative values (α < 0)**—may lead to **sub-optimal power extraction**, even when wind speeds are high.

## 📌 Action Items

- Analyze the LIDAR wind profile data to calculate the wind shear exponent at various times.
- Identify time periods where the shear exponent is **negative or unusually high**.
    - Below 0
    - Above 0.3
    - Between 0-0.3
- Correlate these time periods with turbine power output to confirm if **negative wind shear** is contributing to performance degradation.
- Share findings with the vendor for further analysis or turbine tuning.

![image](https://github.com/user-attachments/assets/157cb099-426a-446c-a1a4-dfe839328a64)

##  📉 Results
These results are when wind speed > cut - in , power genration < 100% and wind turbine is operational
###  Vendor Assumption Doesn’t Hold in All Months

- In **January (1)** and **February (2)**, power generation is **lowest** when wind shear is in the so-called *ideal range* (**0 < α < 0.3**).
- This **contradicts** the vendor’s assumption that power generation should be highest in this range.



###  Vendor Assumption Holds in March and December

- In **March (3)** and **December (12)**, power generation is **highest** when wind shear is within the vendor-suggested range (**0 < α < 0.3**).
- This **supports** the vendor’s assumption for these months.



###  Additional Insight

- Outside the vendor-suggested region (i.e., when shear is **< 0** or **> 0.3**), **power generation is not consistently decreasing**.
- In fact, in several months, these ranges show **equal or better performance** than the ideal shear range.


## Key Inferences

1. **Wind speed** is the main driver—optimize blade angles during low-wind.
2. **Real-time status** flags enable preemptive alerts for outages.
3. **Maintenance during low generation months** minimizes revenue loss.

---

## Future Work

* **Live SCADA integration** for real-time alerts.
* **Predictive maintenance** using sensor and historical data.
* **Farm-level analysis** across multiple turbines.

---
