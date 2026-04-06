📌 Overview 

This project showcases an end‑to‑end automated reporting and visualization pipeline built using Informatica Cloud, Azure SQL Database, ImageCharts.io, and Microsoft Power Automate (MS Flow).

The solution extracts data from a legacy on‑premise system using Informatica Cloud IICS, loads it into Azure SQL tables, and transforms the data into analytical views optimized for downstream visualization. Once the Informatica CAI workflow completes, it triggers a Power Automate flow, which dynamically calls ImageCharts.io using adaptive cards and SQL view outputs to generate real‑time charts. These charts are then automatically delivered to Microsoft Teams channels for business consumption.

This architecture demonstrates expertise in cloud integration, workflow orchestration, API‑driven visualization, and event‑based automation, enabling a fully hands‑free reporting experience for business stakeholders.


🏗️ Enterprise Architecture Diagram

```mermaid
flowchart TB

    %% SOURCE
    subgraph SRC[Source Layer]
        LEGACY[Legacy System<br/>Operational Data]
    end

    %% INFORMATICA CLOUD
    subgraph IICS[Informatica Cloud IICS]
        EXTRACT[Extract Task<br/>Source to Staging]
        LOAD[Load Task<br/>Azure SQL Tables]
        CAI[CAI Process<br/>Workflow Orchestration<br/>Success/Failure Events]
    end

    %% AZURE SQL
    subgraph SQL[Azure SQL Database]
        TABLES[Raw Tables<br/>Loaded by IICS]
        VIEWS[Analytical Views<br/>Chart Input Models]
    end

    %% AUTOMATION
    subgraph FLOW[Microsoft Power Automate]
        TRIGGER[Trigger on CAI Completion]
        QUERY[SQL Query Action<br/>Fetch View Data]
        IMGAPI[Call ImageCharts.io<br/>Generate Chart URL]
        ADAPT[Adaptive Card Builder]
    end

    %% DELIVERY
    subgraph TEAMS[Microsoft Teams Delivery]
        POST[Post Adaptive Card<br/>With Chart Image]
    end

    %% FLOWS
    LEGACY --> EXTRACT
    EXTRACT --> LOAD
    LOAD --> TABLES
    TABLES --> VIEWS
    CAI --> TRIGGER
    TRIGGER --> QUERY
    QUERY --> IMGAPI
    IMGAPI --> ADAPT
    ADAPT --> POST
```
