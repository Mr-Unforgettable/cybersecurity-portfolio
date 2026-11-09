# Task 6 -- Custom SIEM Security Dashboard

A cybersecurity internship project demonstrating a custom SIEM dashboard
using the ELK Stack.

> **Safety and scope:** All attack-related records are synthetic
> simulations created for an educational lab. No real attacks were
> performed and no actual data was exfiltrated.

## Overview

The project ingests, indexes, filters, and visualizes synthetic security
events using Elasticsearch, Logstash, and Kibana.

Dashboard scenarios: - Brute-force login attempts - Privilege-escalation
activity - Potential data exfiltration

## Technology Stack

-   Elasticsearch 8.19.0
-   Logstash 8.19.0
-   Kibana 8.19.0
-   Docker Compose
-   PowerShell
-   JSON Lines

## Dataset Summary

  Scenario                            Events
  --------------------------------- --------
  Normal activity                         30
  Brute-force simulation                  26
  Privilege-escalation simulation         20
  Exfiltration simulation                 20
  **Total**                           **96**

## Architecture

``` text
Synthetic JSON Events
        |
        v
Task 6 Logstash Pipeline
        |
        v
Elasticsearch Index
        |
        v
Kibana Data View
        |
        v
Kibana Lens Visualizations
        |
        v
Custom SIEM Dashboard
```

## Project Structure

``` text
siem-correlation-project/
├── docker-compose.yml
├── data/
│   ├── security-events.json
│   └── task6-dashboard-events.json
├── scripts/
│   └── task6/
│       └── generate-task6-dataset.ps1
├── logstash/
│   ├── pipeline/
│   │   └── logstash.conf
│   └── task6/
│       └── task6-dashboard.conf
├── screenshots/
│   └── task6/
└── report/
    └── task6/
```

## Dataset Fields

Generated events include `@timestamp`, `event_type`, `scenario`,
`event_action`, `event_outcome`, `source_ip`, `username`, `hostname`,
`severity`, `process_name`, `command_line`, `destination_ip`,
`dns_query`, `bytes_out`, and `description`.

## Implementation

### 1. Generate the dataset

Run from the project root in PowerShell:

``` powershell
.\scripts\task6\generate-task6-dataset.ps1
```

Output: `data/task6-dashboard-events.json`

### 2. Validate the Task 6 Logstash pipeline

``` powershell
docker compose run --rm --no-deps logstash-task6 -f /usr/share/logstash/task6/task6-dashboard.conf -t
```

Expected result: `Configuration OK`.

### 3. Ingest the dataset

``` powershell
docker compose up -d --force-recreate logstash-task6
```

Task 6 uses a separate Logstash container so the existing Task 2
Logstash service does not need to be restarted. The file input reads the
dataset and exits after processing it.

### 4. Verify indexed events

``` powershell
Invoke-RestMethod -Uri "http://localhost:9200/siem-dashboard-events-*/_count" -Method Get
```

Expected initial count: `96`.

**Repeat-ingestion note:** The pipeline uses
`sincedb_path => "/dev/null"` and reads from the beginning. Running
ingestion again can index another copy of the events. Check or remove
the previous Task 6 index before repeating ingestion if a clean 96-event
count is required.

### 5. Create the Kibana data view

Open `http://localhost:5601` and create:

-   **Name:** `SIEM Task 6 Events`
-   **Index pattern:** `siem-dashboard-events-*`
-   **Timestamp field:** `@timestamp`

Verify the records in Kibana Discover.

## Dashboard Visualizations

**Dashboard name:** `Task 6 - Custom SIEM Security Dashboard`\
**Time range used for validation:** Last 24 hours

### 1. Brute-force attempts

KQL:

``` text
scenario : "brute_force_simulation" and event_outcome : "failure"
```

-   Chart: Vertical bar
-   Horizontal axis: `source_ip.keyword`
-   Vertical axis: Count of records
-   Observed: 25 failed login events from `203.0.113.45`

The brute-force simulation has 26 events overall, including one
successful authentication event.

### 2. Privilege-escalation activity

KQL:

``` text
scenario : "privilege_escalation_simulation"
```

-   Chart: Vertical bar
-   Horizontal axis: `event_action.keyword`
-   Vertical axis: Count of records
-   Observed: 12 `process_start` and 8 `group_membership_change` events

### 3. Potential data exfiltration

KQL:

``` text
scenario : "exfiltration_simulation" and event_action : "outbound_transfer"
```

-   Chart: Vertical bar
-   Horizontal axis: `destination_ip.keyword`
-   Vertical axis: Sum of `bytes_out`
-   Observed: Approximately 1.23 GB of simulated outbound transfers to
    `198.51.100.25`

## Validation Results

  Validation                    Result
  ----------------------------- -----------
  Logstash configuration test   Passed
  Elasticsearch connectivity    Passed
  Dataset ingestion             Completed
  Indexed event count           96
  Kibana data view              Created
  Discover visibility           Verified
  Three visualizations          Created
  Combined dashboard            Created

## Evidence

Store screenshots in `screenshots/task6/`:

1.  Kibana Discover showing indexed Task 6 events.
2.  Brute-force visualization.
3.  Privilege-escalation visualization.
4.  Exfiltration visualization.
5.  Final combined dashboard.

## Limitations

-   The dataset is synthetic, not real enterprise telemetry.
-   Scenarios are simulations; no actual attack or data exfiltration was
    conducted.
-   The dashboard demonstrates event filtering and aggregation, not
    definitive malicious-activity classification.
-   The lab has Elasticsearch security disabled and is intended only for
    a controlled local environment.
-   Production use would require secure access, role-based permissions,
    real log sources, alerting, retention, and monitoring.

## Author

**Abhinav Pratap Singh**\
Elevance Skills -- Cybersecurity Internship
