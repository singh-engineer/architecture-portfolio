📌 Overview

This project implements an event‑driven, fully automated data processing pipeline integrating AWS services with Snowflake to deliver a scalable, resilient, and production‑ready ingestion framework.

The workflow begins with AWS‑based triggers that invoke Snowflake stored procedures, which generate input files and land them in an S3 bucket. These files serve as the control inputs for an AWS Glue job, orchestrated through AWS Step Functions and scheduled using Amazon EventBridge. Glue executes a custom .jar application, processes the input payloads, and writes enriched output files back into a designated S3 location.

An SNS topic is configured to listen for new output files and automatically notifies Snowpipe, enabling auto‑ingest into Snowflake target tables with minimal latency. This architecture demonstrates strong command of serverless orchestration, event‑based processing, Snowflake integration patterns, and AWS‑native automation, delivering a robust and scalable data pipeline suitable for enterprise workloads.

🏗️ Enterprise Architecture Diagram

```mermaid
flowchart TB

    %% ============================
    %% EVENT SCHEDULING & ORCHESTRATION
    %% ============================
    subgraph ORCH[Orchestration Layer]
        EVBRIDGE[Amazon EventBridge<br/>Scheduled Rule]
        STEP[Step Functions State Machine<br/>Orchestrates Entire Workflow]
        CHK[Status Check & Wait States<br/>Polling / Backoff / Retry]
        ERR[Error Handling<br/>Fallback Paths / Alerts]
    end

    %% ============================
    %% SNOWFLAKE STORED PROCEDURE EXECUTION
    %% ============================
    subgraph SF[Snowflake Layer]
        PROC[Stored Procedure<br/>Generates Control/Input Files]
        S3IN[S3 Input Bucket<br/>Procedure Output Files]
        META[Metadata Tables<br/>Run Status / Audit / Control Flags]
    end

    %% ============================
    %% GLUE PROCESSING LAYER
    %% ============================
    subgraph GLUE[AWS Glue Processing Layer]
        GLUEJOB[Glue Job<br/>Executes .jar Application]
        GLUELOGS[Glue Logs<br/>CloudWatch Metrics]
        S3OUT[S3 Output Bucket<br/>Processed Files]
    end

    %% ============================
    %% SNS + SNOWPIPE INGESTION
    %% ============================
    subgraph INGEST[Snowpipe Auto-Ingest Layer]
        SNS[SNS Topic<br/>S3 Event Notifications]
        SNOWPIPE[Snowpipe<br/>Auto-Ingest Pipeline]
        OUTTABLES[Snowflake Target Tables<br/>Curated Output]
    end

    %% ============================
    %% OBSERVABILITY & GOVERNANCE
    %% ============================
    subgraph OBS[Observability & Governance]
        CW[CloudWatch Logs & Metrics<br/>Glue / Step Functions / S3 Events]
        SFMON[Snowflake Monitoring<br/>Load History / Pipe Status]
        AUDIT[Audit & Lineage<br/>Run IDs / Timestamps / Checksums]
    end

    %% ============================
    %% FLOWS
    %% ============================

    EVBRIDGE --> STEP
    STEP --> PROC
    PROC --> META
    PROC --> S3IN

    STEP --> CHK
    CHK --> GLUEJOB
    GLUEJOB --> GLUELOGS
    GLUEJOB --> S3OUT

    S3OUT --> SNS
    SNS --> SNOWPIPE
    SNOWPIPE --> OUTTABLES

    %% OBSERVABILITY CONNECTIONS
    STEP --> CW
    GLUEJOB --> CW
    S3OUT --> CW
    SNOWPIPE --> SFMON
    META --> AUDIT
    OUTTABLES --> AUDIT

    %% ERROR HANDLING
    STEP --> ERR
    GLUEJOB --> ERR
```
