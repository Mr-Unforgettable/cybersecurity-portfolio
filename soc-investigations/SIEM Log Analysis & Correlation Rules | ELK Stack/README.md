# SIEM Log Analysis & Correlation Rules | ELK Stack

## Project Overview

This project was completed as part of the Elevance Skills Cybersecurity Internship.

The objective was to build and test a Security Information and Event Management (SIEM) environment using the **Elastic Stack (Elasticsearch, Logstash, and Kibana)** and develop detection logic for selected security scenarios.

The project uses synthetic security telemetry to demonstrate log ingestion, analysis, and detection workflows in a controlled lab environment.

## Technology Stack

- Elasticsearch 8.19.0
- Logstash 8.19.0
- Kibana 8.19.0
- Docker Desktop
- Docker Compose
- Windows 11 with WSL2
- PowerShell

## Detection Scenarios

Three security scenarios were simulated and investigated:

| Scenario | Simulated Events |
|---|---:|
| Credential stuffing | 20 |
| DNS tunnelling | 20 |
| Suspicious PowerShell activity | 20 |

The dataset also included benign authentication, DNS, and process activity for comparison.

## Implementation

1. Deployed Elasticsearch, Logstash, and Kibana using Docker Compose.
2. Generated synthetic security events in JSON Lines format.
3. Configured Logstash to ingest and enrich the events.
4. Indexed the processed events in Elasticsearch.
5. Created a Kibana data view to explore the security telemetry.
6. Queried the dataset to identify the three simulated detection scenarios.
7. Verified the results using Elasticsearch queries, Kibana Discover, and Logstash output.

## Results

- Elasticsearch reported **212 indexed documents** at the time of validation.
- The generated dataset contained **210 intended events**; the two additional indexed documents were not conclusively attributed.
- Kibana Discover displayed 190 documents in the selected time range.
- Each of the three simulated detection scenarios returned 20 matching events.

These counts refer to different validation scopes and should not be treated as interchangeable.

## Repository Contents

```text
siem-correlation-project/
├── data/
├── logstash/
│   └── pipeline/
├── report/
├── rules/
├── screenshots/
├── scripts/
└── README.md
```

## Project Report

The detailed internship project report, including architecture, implementation steps, validation results, screenshots, limitations, and conclusions, is available here:

**[SIEM Custom Correlation Rule (PDF)](SIEM_Task2_Report.pdf)**

## Important Notes

- All event data used in this project is synthetic and intended for educational purposes.
- No real-world systems were attacked.
- The detection logic demonstrates scenario-based filtering and validation. It is not a production-ready, time-windowed correlation or automated alerting system.
- The project is intended as a learning demonstration of SIEM log ingestion, analysis, and detection workflows.

## Learning Outcomes

- Understanding SIEM architecture and log pipelines.
- Deploying the Elastic Stack with Docker Compose.
- Ingesting and analyzing structured security events.
- Investigating authentication, DNS, and process telemetry.
- Developing and validating basic detection logic.
- Documenting security monitoring results and limitations.

---

**Project Status:** Completed  
**Internship:** Elevance Skills – Cybersecurity
