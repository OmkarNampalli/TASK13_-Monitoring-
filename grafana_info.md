# Grafana
Grafana is the piece that makes all those Prometheus metrics actually make *sense to humans*. Let’s walk through it clearly, no fluff.

## What is Grafana?

**Grafana is an open-source data visualization and analytics platform.**

In simple terms:

> Grafana turns raw data into dashboards, graphs, and alerts.

Grafana does not collect data by itself.

It connects to data sources (like Prometheus) and visualizes what’s already there.

## What is Grafana used for?
Grafana is used to:
* Visualize metrics and trends
* Build real-time dashboards
* Monitor system and application health
* Create alerts based on data
* Share dashboards with teams

### Common use cases
* Infrastructure monitoring (CPU, memory, disk)
* Application performance monitoring (APM)
* Kubernetes observability
* Business metrics (orders, revenue, traffic)
* Incident debugging & postmortems

**If Prometheus is the engine, Grafana is the dashboard and speedometer.**

## What kind of data does Grafana use?
Grafana works with many data sources, not just Prometheus.

Popular ones:
* Prometheus (metrics)
* Loki (Logs)
* Tempo / Jaeger (traces)
* Elasticsearch
* InfluxDB
* MySQL / PostgreSQL
* CloudWatch / Azure Monitor

Grafana unifies them into one UI.

## How Grafana works (big picture)
Here’s the high-level flow:
```
[ Data Source ]
   (Prometheus, Loki, DB)
          ↓
      Grafana
          ↓
[ Dashboards / Alerts / Users ]
```

Let’s break it down.

### 1. Data sources (read-only)

Grafana queries data sources.

Example:

Prometheus stores metrics

Grafana asks Prometheus:

*“Give me CPU usage for the last 1 hour”*

**Grafana never stores metrics long-term — it’s a visualization layer.**

### 2. Queries (language depends on data source)

Each data source has its own query language:
* Prometheus → PromQL
* Loki → LogQL
* SQL DBs → SQL

Example PromQL inside Grafana:
```
rate(http_requests_total[5m])
```

Grafana sends this query to Prometheus and gets back time-series data.

### 3. Panels (building blocks of dashboards)

A panel is a single visualization.

Common panel types:
* Line graph
* Bar chart
* Gauge
* Heatmap
* Table
* Single stat

Each panel:
* runs a query
* visualizes the result
* refreshes automatically

### 4. Dashboards (collections of panels)

A dashboard is a group of panels on one screen.

Example dashboard:
* CPU usage (line graph)
* Memory usage (gauge)
* Disk space (bar)
* Network traffic (graph)

Dashboards can be:
* real-time
* time-range based
* shared with links
* exported/imported as JSON

### 5. Variables (dynamic dashboards)

Grafana supports dashboard variables.

Example:
```
server = server1, server2, server3
```
Select a server from a dropdown →

all panels update automatically.

This makes dashboards reusable and powerful.

### 6. Alerts in Grafana

Grafana can create alerts based on queries.

Example:
> Alert me if CPU > 90% for 5 minutes

Grafana alerts can:
* notify Slack, email, PagerDuty
* work across data sources
* be managed centrally

(Though Prometheus + Alertmanager is still very common.)

## How Grafana is used in real life
**Example 1: Prometheus + Grafana (most common)**
* Prometheus collects metrics
* Grafana connects to Prometheus
* Dashboards show:
* CPU, memory, disk
* request rates
* error rates
* Alerts notify when something breaks

This combo is everywhere in DevOps and SRE.

**Example 2: Kubernetes monitoring**

Grafana shows:
* cluster health
* pod CPU/memory
* node usage
* API server latency
> Most teams import pre-built dashboards instead of starting from scratch.

**Example 3: Logs + metrics correlation**

Grafana + Loki + Prometheus:
* See a spike in errors (metric)
* Click → view related logs
* Debug faster

This is called observability, not just monitoring.

## How Grafana is typically set up

Basic flow:
```
Install Grafana
↓
Add data source (Prometheus)
↓
Import dashboards
↓
Customize panels
↓
Add alerts
↓
Share with team
```

Grafana runs on:
* Linux
* Docker
* Kubernetes
* Cloud (Grafana Cloud)

## What Grafana is NOT

Important clarity:

Grafana is not:
* a metrics collector
* a database
* a log shipper

It reads data, visualizes it, and alerts on it.

## One-sentence summary

> **Grafana visualizes and analyzes data from sources like Prometheus, turning metrics into dashboards, graphs, and alerts humans can understand.**

---

# Prometheus vs Grafana
| Prometheus         | Grafana                    |
| ------------------ | -------------------------- |
| Collects metrics   | Visualizes data            |
| Stores time-series | Queries data               |
| Has PromQL         | Uses many query languages  |
| Does alerting      | Does alerting + dashboards |

They’re teammates, not competitors.
