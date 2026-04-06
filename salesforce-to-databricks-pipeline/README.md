📌 Overview
This project implements a metadata‑driven, incremental ingestion and transformation framework designed to move complex Salesforce datasets into a scalable, governed, and analytics‑ready Delta Lake architecture on Databricks.

The solution orchestrates the extraction of 30+ Salesforce objects using Azure Data Factory, applies dynamic parameterization for object‑specific logic, and lands raw Parquet files into Azure Data Lake Storage Gen2 with enterprise‑grade partitioning, audit logging, and schema drift handling.

From there, Databricks processes the data through the Bronze → Silver → Gold medallion layers, applying robust data quality checks, lineage tracking, and business modeling to deliver trusted, production‑ready datasets for downstream analytics and reporting.

This architecture reflects modern data engineering best practices, including incremental ingestion, Delta Lake ACID guarantees, observability, error‑resilient orchestration, and layer‑aware governance across the entire data lifecycle.

## 🏗️ Enterprise Architecture Diagram (Advanced)

```mermaid
flowchart TB

    %% LAYERS
    subgraph SRC[Source Layer]
        SF[Salesforce<br/>30+ Objects]
    end

    subgraph ING[Ingestion & Orchestration Layer]
        ADF[Azure Data Factory<br/>Orchestration]
        META[Metadata Store<br/>Object Configs<br/>Watermark Values]
        ERR[Error Handling<br/>Retry Logic<br/>Failure Alerts]
    end

    subgraph RAW[Landing Zone - ADLS Gen2]
        RAWF[Raw Parquet Files<br/>Partitioned Storage]
        AUDIT[Audit Logs<br/>Ingestion Metrics]
    end

    subgraph BRZ[Databricks Bronze Layer]
        BRZJOB[Bronze Ingestion Job<br/>Schema Normalization<br/>Delta Lake]
        BRZDLT[Bronze Delta Tables]
    end

    subgraph SIL[Databricks Silver Layer]
        SILJOB[Silver Transformations<br/>Cleansing & Dedup<br/>Type Casting]
        SILDLT[Silver Delta Tables]
    end

    subgraph GLD[Databricks Gold Layer]
        GLDJOB[Gold Modeling<br/>Dimensions & Facts<br/>Aggregations]
        GLDDLT[Gold Delta Tables]
    end

    subgraph OBS[Observability & Governance]
        DQ[Data Quality Checks<br/>Great Expectations / Custom Rules]
        LINEAGE[Data Lineage<br/>Table Dependencies]
        MON[Monitoring<br/>ADF + Databricks Logs]
    end

    %% FLOWS
    SF --> ADF
    ADF --> META
    ADF --> RAWF
    ADF --> ERR
    RAWF --> BRZJOB
    BRZJOB --> BRZDLT
    BRZDLT --> SILJOB
    SILJOB --> SILDLT
    SILDLT --> GLDJOB
    GLDJOB --> GLDDLT

    %% OBSERVABILITY CONNECTIONS
    BRZJOB --> DQ
    SILJOB --> DQ
    GLDJOB --> DQ

    BRZDLT --> LINEAGE
    SILDLT --> LINEAGE
    GLDDLT --> LINEAGE

    ADF --> MON
    BRZJOB --> MON
    SILJOB --> MON
    GLDJOB --> MON
```

