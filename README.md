# Data Quality & Trust in Production
Ingestion Monitoring for PJM Instantaneous Load (PJM RTO)

## Overview

This project demonstrates how to design ingestion-layer data quality and trust
monitoring for real-world operational telemetry.

Using PJM Instantaneous Load (5-minute system-level data), the focus is on
detecting *silent data failures* that can compromise downstream analytics and
forecasting systems if left unnoticed.

Rather than assuming data is trustworthy, the system continuously evaluates
freshness, completeness, plausibility, and signal health, and explicitly decides
when downstream systems must be blocked.

---

## Why This Project Exists

Most production incidents are not caused by models.  
They are caused by bad or stale data that still looks valid.

Common failure modes include:
- Data arriving late but appearing complete
- Missing intervals hidden by aggregation
- Flatlined telemetry that passes basic checks
- Subtle timestamp misalignment

This project answers a critical operational question:

**How do we know whether incoming data can be trusted right now?**

---

## Data Source

**Dataset:** PJM Instantaneous Load  
**Source:** PJM Data Miner 2  
**Granularity:** 5-minute telemetry  
**Scope:** PJM RTO (system-level)  
**Nature:** Operational, unverified, near-real-time data  

This dataset is intentionally used because it represents ingestion-layer
telemetry, not cleaned settlement data.

---

## Ingestion Assumptions

The system assumes:
- Exactly one record every 5 minutes
- Timestamps aligned to 5-minute boundaries
- Data freshness within operational tolerance
- Load values are physically plausible

Violations of these assumptions are treated as **alerts**, not silent corrections.

---

## Core Data Quality Checks

### 1. Timestamp Alignment
Detects whether timestamps align exactly to 5-minute boundaries.

Misalignment breaks joins, resampling, and downstream models and is a common
real-world ingestion failure.

---

### 2. Interval Completeness
Ensures exactly one record exists for every expected 5-minute interval.

**Thresholds**
- 0% missing → Healthy
- 0.5% missing → Warning
- 2% missing → Critical (block downstream)

---

### 3. Freshness (Most Critical Check)
Measures lag between the latest data point and “now”.

**Thresholds**
- 10 minutes → Warning
- 20 minutes → Critical (block downstream)

**Key insight:**  
Late data is more dangerous than missing data because it looks complete.

---

### 4. Plausibility Rules
Rules enforced:
- Load must be non-negative
- Zero values are flagged

Rule-based checks catch catastrophic failures that statistics miss.

---

### 5. Flatline Detection
Detects sustained periods of no meaningful change in load.

Logic:
- 6 consecutive 5-minute intervals (~30 minutes)
- Near-zero delta

Flatlines often indicate sensor, API, or upstream system failure.

---

## Alerting Philosophy

All checks feed into a structured alert table with:
- Check name
- Severity (info / warning / critical)
- Metric
- Value

| Severity | Meaning              | Action                  |
|--------|----------------------|-------------------------|
| Info   | Minor anomaly        | Log only                |
| Warning| Degraded quality     | Monitor                 |
| Critical | Data not trustworthy | Block downstream systems |

This mirrors real production decision logic.

---

## Key Insight Demonstrated

A dataset can be:
- Highly complete
- Statistically clean

…and still be **operationally unusable**.

Reliable analytics begin with reliable ingestion.

---

## Repository Structure

```text
data-quality-trust/
├── notebooks/   # ingestion monitoring logic
├── data/        # raw telemetry (excluded)
├── outputs/     # alerts and diagnostics

## Author

Yeji Choe  
Data Scientist – Applied Forecasting & Reliability  
Auckland, New Zealand