## Data

This project evaluates ingestion-layer data quality for near-real-time telemetry.

Raw data is intentionally **not committed** to version control.

---

## Raw Data

**Source:** PJM Data Miner 2  
**Dataset:** Instantaneous Load  
**Granularity:** 5-minute telemetry  
**Scope:** PJM RTO (system-level)

This dataset represents unverified operational telemetry, not settlement data.

Raw CSV exports should be placed locally in:


These files are excluded via `.gitignore`.

---

## Why Raw Data Is Excluded

- Telemetry data is large and frequently updated
- Redistribution may be restricted
- In production, ingestion systems read from external sources, not Git

This mirrors real ingestion architecture.

---

## Reproducibility

To reproduce results:
1. Download Instantaneous Load CSVs from PJM Data Miner 2
2. Place them in `data/raw/`
3. Run `notebooks/ingestion_monitoring.ipynb`
