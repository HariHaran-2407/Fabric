# GitHub Data Pipeline — Microsoft Fabric (Medallion Architecture)

An end-to-end ETL pipeline built on **Microsoft Fabric** that ingests data from the GitHub REST API and processes it through a full medallion architecture (Bronze → Silver → Gold).

---

## Architecture Overview

```
GitHub REST API
      │
      ▼
┌─────────────┐
│   BRONZE    │  ← Raw files (Lakehouse)
│  Lakehouse  │     Ingested via HTTPS API
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   SILVER    │  ← Cleaned & transformed tables (Lakehouse)
│  Lakehouse  │     Transformations applied, saved as Delta tables
└──────┬──────┘
       │
       ▼
┌─────────────┐
│    GOLD     │  ← Aggregated views (Warehouse)
│  Warehouse  │     CTAS queries + views across multiple tables
└─────────────┘
```

---

## Layers

### Bronze Layer — Raw Ingestion (Lakehouse)

The ingestion pipeline connects to the **GitHub REST API** via HTTPS and uses a **For Each** activity to loop through and pull **4 data files**, loading each one into the **Bronze Lakehouse** on OneLake.

#### For Each Activity
<img width="1919" height="885" alt="Screenshot 2026-04-01 202225" src="https://github.com/user-attachments/assets/c796be6c-89a0-4199-b618-80df3733cd99" />

#### Copy Data Inside For Each
<img width="1919" height="882" alt="Screenshot 2026-04-01 202303" src="https://github.com/user-attachments/assets/88aa39f3-fc4f-4c26-bd19-dc8206ff45a0" />

#### Bronze Lakehouse File Structure
<img width="1918" height="873" alt="Screenshot 2026-04-01 202347" src="https://github.com/user-attachments/assets/685be018-139f-47e5-a54a-3273fc0e695a" />

---

### Silver Layer — Transformation (Lakehouse)

All transformations are handled in a **single Fabric Notebook**:
- Data type casting
- Null handling and data cleaning
- Column renaming and standardization

Processed data is saved as **Delta tables** in the Silver Lakehouse.

#### Silver Lakehouse File Structure
<img width="1919" height="881" alt="Screenshot 2026-04-01 202428" src="https://github.com/user-attachments/assets/b31d99d7-3107-4cbf-b6af-36628c7b5251" />

---

### Gold Layer — Aggregation (Warehouse)

- Created a **Gold Warehouse** as the serving layer
- Loaded data from Silver using **CTAS (Create Table As Select)**
- Built an **aggregated view** joining 2 tables for business-level reporting

#### Gold Data Warehouse Structure
<img width="1915" height="880" alt="Screenshot 2026-04-01 202506" src="https://github.com/user-attachments/assets/7b754bfc-8b66-4dc1-b40e-facdf503bc6a" />

---

## Lineage

### Raw Data Ingestion Lineage
<img width="1653" height="636" alt="Screenshot 2026-04-01 212022" src="https://github.com/user-attachments/assets/35658bdc-31c7-4163-bc7a-d888b060ae96" />


### Overall Pipeline Lineage
<img width="1436" height="629" alt="Screenshot 2026-04-01 212100" src="https://github.com/user-attachments/assets/21f7566b-385c-49d7-8a5f-641844a7173d" />


---

## Tech Stack

| Tool | Purpose |
|---|---|
| Microsoft Fabric | Unified data platform |
| Fabric Data Pipeline | Orchestration with For Each + Copy Data |
| Fabric Notebook | All data transformations (Bronze → Silver → Gold) |
| OneLake | Unified storage across layers |
| Lakehouse (Bronze & Silver) | Raw and cleaned data storage |
| Warehouse (Gold) | Serving layer with SQL analytics |
| GitHub REST API | Data source |
| Delta Tables | Storage format in Silver layer |
| CTAS / SQL Views | Gold layer aggregation logic |

---

## Pipeline Flow

1. **Ingest** — Data pipeline uses For Each activity to loop through 4 GitHub API endpoints; Copy Data loads each file into Bronze Lakehouse
2. **Transform** — Single Fabric Notebook reads Bronze files, applies all transformations, and saves as Delta tables in Silver Lakehouse
3. **Serve** — SQL scripts load Silver data into Gold Warehouse using CTAS; aggregated view created across 2 tables for reporting

---

## Key Concepts Demonstrated

- Medallion Architecture (Bronze / Silver / Gold)
- REST API ingestion using HTTPS
- Pipeline orchestration with For Each and Copy Data activities
- Delta table storage and management
- SQL transformations with CTAS and Views
- Separation of Lakehouse vs. Warehouse concerns in Microsoft Fabric
- OneLake as a unified storage layer across pipeline stages
