📌 Overview 

This project showcases an end‑to‑end automated reporting and visualization pipeline built using Informatica Cloud, Azure SQL Database, ImageCharts.io, and Microsoft Power Automate (MS Flow).

The solution extracts data from a legacy on‑premise system using Informatica Cloud IICS, loads it into Azure SQL tables, and transforms the data into analytical views optimized for downstream visualization. Once the Informatica CAI workflow completes, it triggers a Power Automate flow, which dynamically calls ImageCharts.io using adaptive cards and SQL view outputs to generate real‑time charts. These charts are then automatically delivered to Microsoft Teams channels for business consumption.

This architecture demonstrates expertise in cloud integration, workflow orchestration, API‑driven visualization, and event‑based automation, enabling a fully hands‑free reporting experience for business stakeholders.


🏗️ Enterprise Architecture Diagram

```mermaid
flowchart TB

    %% ============================
    %% SOURCE SYSTEM
    %% ============================
    subgraph SRC[Source Layer]
        LEGACY[Legacy System<br/>Operational Data]
        METADATA[Metadata & Control Tables<br/>Run Status / Audit Flags]
    end

    %% ============================
    %% INFORMATICA CLOUD
    %% ============================
    subgraph IICS[Informatica Cloud]
        EXTRACT[Extract Task<br/>Source to Staging]
        LOAD[Load Task<br/>Azure SQL Tables]
        CAI[CAI Workflow<br/>Orchestration Events]
        ERR[Error Handling<br/>Retry Logic / Notifications]
    end

    %% ============================
    %% AZURE SQL DATABASE
    %% ============================
    subgraph SQL[Azure SQL Database]
        STAGING[Staging Tables<br/>Raw Loaded Data]
        VIEWS[Analytical Views<br/>Chart Input Models]
        DQ[Data Quality Checks<br/>Validation Rules]
        AUDIT[Audit Tables<br/>Run IDs / Checksums]
    end

    %% ============================
    %% POWER AUTOMATE
    %% ============================
    subgraph FLOW[Power Automate Flow]
        TRIGGER[Trigger on CAI Completion]
        QUERY[SQL Query Action<br/>Fetch View Data]
        FORMAT[Data Formatting<br/>JSON Payload Builder]
        IMGAPI[ImageCharts API<br/>Generate Chart URL]
        ADAPT[Adaptive Card Builder<br/>Embed Chart]
        FLOWLOGS[Flow Logs<br/>Run History]
    end

    %% ============================
    %% TEAMS DELIVERY
    %% ============================
    subgraph TEAMS[Microsoft Teams]
        POST[Post Adaptive Card<br/>Chart Delivered]
    end

    %% ============================
    %% OBSERVABILITY
    %% ============================
    subgraph OBS[Observability & Monitoring]
        IICSLOGS[IICS Logs<br/>Task Runs / Failures]
        SQLMON[SQL Monitoring<br/>Query Performance]
        FLOWMON[Flow Monitoring<br/>Run Status]
        ALERTS[Alerts & Notifications]
    end

    %% ============================
    %% FLOWS
    %% ============================

    LEGACY --> EXTRACT
    EXTRACT --> LOAD
    LOAD --> STAGING
    STAGING --> VIEWS
    VIEWS --> DQ
    DQ --> AUDIT

    CAI --> TRIGGER
    TRIGGER --> QUERY
    QUERY --> FORMAT
    FORMAT --> IMGAPI
    IMGAPI --> ADAPT
    ADAPT --> POST

    %% OBSERVABILITY CONNECTIONS
    EXTRACT --> IICSLOGS
    LOAD --> IICSLOGS
    VIEWS --> SQLMON
    QUERY --> FLOWMON
    ADAPT --> FLOWMON
    FLOWMON --> ALERTS
    IICSLOGS --> ALERTS
    SQLMON --> ALERTS

    %% ERROR HANDLING
    CAI --> ERR
    ERR --> ALERTS
```
