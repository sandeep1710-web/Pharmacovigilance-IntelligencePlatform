# Pharmacovigilance Intelligence Platform
### From Raw Adverse Event Reports to AI-Powered Safety Signals

An end-to-end data engineering and analytics capstone demonstrating how adverse event (AE) reports can move from cloud object storage through data quality processing and a cloud data warehouse into machine-learning-assisted signal detection and business intelligence.

> **Project author:** Y. Siva Sandeep  
> **Domain:** Pharmacovigilance / Healthcare Data Analytics  
> **Primary technologies:** Amazon S3, AWS IAM, Dataiku DSS, Snowflake, Snowflake Cortex ML, Power BI, SQL

## Project at a glance

The project documentation describes an assignment dataset containing **83 records and 19 columns**, representing adverse event reports from a legacy safety system. Fields include report dates, patient demographics, suspected drug, adverse-event term, seriousness, outcome, manufacturing batch, dosage, and causality assessment.

The pipeline is organized into these stages:

1. **Cloud landing zone:** Store the source CSV in Amazon S3.
2. **Data preparation:** Use Dataiku DSS to profile, cleanse, standardize, and validate the incoming data.
3. **Warehouse ingestion:** Load data into Snowflake's RAW, STAGING, and CURATED schemas.
4. **Dimensional modelling:** Build a star schema around `FACT_AE_REPORT` and supporting dimensions.
5. **Signal detection:** Aggregate adverse-event counts and use Snowflake Cortex ML anomaly detection.
6. **Business intelligence:** Present curated data and anomaly outputs in Power BI dashboards.

## Architecture

```text
Legacy Safety System
      |
      | CSV adverse-event reports
      v
Amazon S3 (raw landing zone)
      |
      | Snowflake storage integration + external stage
      v
Snowflake RAW
      |
      v
Dataiku DSS
  - Data profiling
  - Duplicate handling
  - Missing-value treatment
  - Text standardization
  - Date normalization
  - Data-quality checkpoint
      |
      v
Snowflake STAGING
      |
      v
Snowflake CURATED
  - FACT_AE_REPORT
  - DIM_DRUG
  - DIM_PATIENT
  - DIM_COUNTRY
  - DIM_MANUFACTURING
  - DIM_DATE
      |
      +------------------------------+
      |                              |
      v                              v
Monthly / weekly AE aggregates   Power BI reporting
      |
      v
Snowflake Cortex ML
(Anomaly Detection)
      |
      v
CURATED.AE_SIGNAL_FLAGS
      |
      v
Power BI safety monitoring dashboards
```

## Technology and responsibilities

| Component | Technology | Role in the project |
|---|---|---|
| Source data | CSV | Adverse event report input |
| Cloud storage | Amazon S3 | Stores the raw source file |
| Access management | AWS IAM | Enables role-based access for Snowflake |
| ETL and data quality | Dataiku DSS | Profiling, cleansing, standardization, validation |
| Data warehouse | Snowflake | RAW, STAGING, and CURATED data layers |
| Data modelling | SQL / Star schema | Fact and dimension tables for analytics |
| Machine learning | Snowflake Cortex ML | Time-series anomaly detection |
| Visualization | Power BI | Interactive safety analytics dashboards |

## Data quality work

The documentation records these source-data issues and treatments:

| Issue | Documented treatment |
|---|---|
| Duplicate `REPORT_ID` values | Retain the first occurrence and remove subsequent duplicates |
| Missing `PATIENT_AGE` | Median imputation |
| Missing `DOSE_AMOUNT_MG` | Retain as `NULL` |
| Missing or undefined `PATIENT_SEX` | Standardize to `U` (Unknown) |
| Mixed categorical text casing | Standardize drug, country, reporter, sex, and manufacturing values |
| Non-standard date representations | Normalize `REPORT_RECEIVED_DATE` and `ONSET_DATE` to date format |
| Data quality validation | Check unique report identifiers before export to STAGING |

The documented Dataiku output destination is `PV_DB.STAGING.AE_REPORTS_FINAL_STAGING`.

## Snowflake data model

The documented environment includes warehouse `PV_WH`, database `PV_DB`, and schemas `RAW`, `STAGING`, and `CURATED`.

**Fact table:** `FACT_AE_REPORT` — one row per adverse event report.

**Dimension tables:** `DIM_DRUG`, `DIM_PATIENT`, `DIM_COUNTRY`, `DIM_MANUFACTURING`, and `DIM_DATE`.

The project documentation also describes referential-integrity checks and analytical views:

- `VW_AE_COUNTS_BY_DRUG_MONTH`
- `VW_SERIOUS_EVENT_RATE_BY_COUNTRY`

## Snowflake Cortex ML

The project aggregates adverse-event reports into monthly and weekly counts by drug. The monthly training and prediction windows documented are:

- **Training:** January 2025 – September 2025
- **Prediction:** October 2025 – December 2025
- **Prediction interval:** 0.95 (95%)

The documented model is `SNOWFLAKE.ML.ANOMALY_DETECTION`, configured with series column `DRUG_NAME`, timestamp column `REPORT_MONTH`, and target column `AE_COUNT`.

The documented output table is `CURATED.AE_SIGNAL_FLAGS`, with forecast, lower/upper bounds, actual count, and anomaly flag fields.

**Important interpretation:** an anomaly is a statistical signal for further review; it does not establish that a drug caused an adverse event. Clinical causality requires independent clinical assessment.

## Screenshots and evidence

Screenshots extracted from the supplied project documentation PDF are organized by platform:

- [Architecture](screenshots/architecture/)
- [AWS](screenshots/aws/)
- [Dataiku DSS](screenshots/dataiku/)
- [Snowflake](screenshots/snowflake/)
- [Power BI](screenshots/powerbi/)

### Power BI dashboards

**Drug-wise monthly dashboard**

![Drug-wise monthly dashboard](screenshots/powerbi/page-47-image-43.png)

**Drug-wise weekly dashboard**

![Drug-wise weekly dashboard](screenshots/powerbi/page-48-image-44.png)

## Repository structure

```text
Pharmacovigilance-Intelligence-Platform/
├── README.md
├── .gitignore
├── documentation/
│   └── project-documentation.pdf
└── screenshots/
    ├── architecture/
    ├── aws/
    ├── dataiku/
    ├── snowflake/
    └── powerbi/
```

## How to explore

1. Start with the architecture section in this README.
2. Browse the platform-specific screenshot folders.
3. Open `documentation/project-documentation.pdf` for the full 48-page implementation report.
4. Review the data model, data-quality rules, anomaly-detection design, and dashboards.

## Scope and reproducibility

This repository documents the implementation and includes screenshots extracted from the supplied report. It does **not** claim to contain deployable AWS infrastructure, Dataiku project exports, Snowflake account objects, Power BI source files, or the original dataset. Those artifacts should be added only if they are available and safe to publish.

The source dataset is described as an assignment dataset. Do not add patient-identifiable or confidential data. Keep cloud credentials, account identifiers, private endpoints, and secrets out of screenshots and source files.

## Author

**Y. Siva Sandeep**

Aspiring Data Engineer | AI & Data Science Enthusiast
