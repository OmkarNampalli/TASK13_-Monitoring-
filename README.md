# TASK13_(Monitoring)
Introduction to server monitoring using prometheus and grafana.

When a system runs, it constantly emits signals about what it’s doing.
The three main observability signals are:
1. **Metrics**
2. **Logs**
3. **Traces**

---

# Prometheus
In the world of modern software, Prometheus is essentially the "heart rate monitor" for your applications and servers. Originally developed at SoundCloud, it’s now a leading open-source monitoring and alerting toolkit designed specifically for dynamic, cloud-native environments like Kubernetes.

Prometheus is a **Time-Series Database (TSDB).**

To understand what prometheus does; we need to understand what are metrics and logs.

**Metrics** tell you that something is wrong, and **Logs** tell you why it is wrong.
## Metrics
**Metrics are numerical measurements collected over time.**

They answer:

> *How is my system behaving in general?*
### Key Characteristics of Metrics:
| Property  | Metrics                        |
| --------- | ------------------------------ |
| Data type | Numeric                        |
| Structure | Time-series                    |
| Frequency | Collected at regular intervals |
| Storage   | Aggregated & compressed        |
| Cost      | Cheap                          |
| Purpose   | Monitoring, alerting           |

### Each metric has:
* Name
* Value
* Timestamp
* Labels (dimensions)

Example (Prometheus-style):
```Bash
http_requests_total{method="GET", status="200"} 15432
```
### Why Metrics Exist

Metrics are designed to:
* Show trends
* Detect anomalies
* Trigger alerts
* Be cheap to store long-term

You don’t need every detail — you need the shape of behavior.

### What Metrics Are Good At

✅ Is the system healthy?
✅ Is traffic increasing?
✅ Did latency spike?
✅ Should I scale?

## Logs
**Logs are detailed, discrete records of events that happened in the system.**

They answer:

> *What exactly happened?*
### Key Characteristics of Logs
| Property  | Logs                |
| --------- | ------------------- |
| Data type | Text / JSON         |
| Structure | Event-based         |
| Frequency | Generated on events |
| Storage   | Large, expensive    |
| Purpose   | Debugging, auditing |

### Example Logs
**Plain text log**
```
2026-02-06 10:21:33 ERROR Database connection failed
```
**Structured (JSON) log**
```
{
  "timestamp": "2026-02-06T10:21:33Z",
  "level": "error",
  "service": "checkout",
  "error": "timeout connecting to db"
}
```
### Why Logs Exist
Logs capture:
* Errors
* Warnings
* User actions
* Stack traces
* Security events

Logs give context, not trends.

### What Logs Are Good At
✅ Debugging failures
✅ Root cause analysis
✅ Security audits
✅ Compliance

## Metrics vs Logs (Side-by-Side)
| Aspect            | Metrics                 | Logs             |
| ----------------- | ----------------------- | ---------------- |
| Question answered | “How much / how often?” | “What happened?” |
| Data volume       | Low                     | High             |
| Storage cost      | Low                     | High             |
| Alerting          | Excellent               | Poor             |
| Debugging         | Limited                 | Excellent        |
| Example tool      | Prometheus              | ELK / Loki       |

## Prometheus
**Prometheus is an open-source monitoring and alerting system.**

It’s mainly used to:
* Collect metrics (numbers over time)
* Store them as time-series data
* Query and visualize system behavior
* Trigger alerts when something looks wrong
It was originally built at **SoundCloud** and is now a **CNCF project** (same family as Kubernetes).

### Prometheus answers questions like:
* Is my server CPU too high?
* Is my app responding slowly?
* Are requests failing?
* Is my database healthy?
* Did something break after a deploy?
### Common use cases
* Infrastructure monitoring (CPU, memory, disk, network
* Application monitoring (latency, errors, request rate)
* Kubernetes monitoring
* Alerting & incident response
* Capacity planning & performance tuning
**Think of Prometheus as:**
> A constantly running lie detector for your systems.

### How Prometheus works (big picture)
Here’s the flow 👇
```
[ App / Server ]
      ↑
   (metrics)
      ↑
[ Exporter ]
      ↑
   (HTTP pull)
      ↑
[ Prometheus ]
      ↓
[ Query / Alerts / Grafana ]
```

Let’s break that down.

#### 1. Metrics exposure
Your app or system exposes metrics over HTTP, usually at:
`
/metrics
`
Example output:
```
cpu_usage 0.72
memory_usage_bytes 534773760
```

This is a plain-text format Prometheus understands.

#### 2. Exporters (very important)
**Most systems don’t speak Prometheus natively.**

So we use **exporters**.
Exporters are small programs that:
* collect metrics from a system
* expose them in Prometheus format
Common exporters:
* **node_exporter** → Linux servers
* **kube-state-metrics** → Kubernetes
* **mysqld_exporte**r → MySQL
* **postgres_exporter** → PostgreSQL
* **blackbox_exporter** → HTTP/DNS/TCP checks

#### 3. Prometheus pulls metrics (pull model)
Prometheus pulls metrics on a schedule:
```
scrape_interval: 15s
```
Every 15 seconds:

Prometheus calls http://target:port/metrics

Parses the numbers

Stores them in its **time-series database (TSDB)**

This pull model is a big design choice and makes Prometheus:

* simpler

* more reliable

* easier to debug
#### 4. Storage (Time-Series Database)
Prometheus stores data as:
```
(metric_name + labels) → value over time
```
Example:
```
cpu_usage{instance="server1"} → [0.4, 0.6, 0.7, ...]
```
It’s optimized for:
* fast writes
* fast time-range queries
* recent data (not long-term storage by default)
#### 5. Querying with PromQL
Prometheus has its own query language: **PromQL**

Examples:

CPU usage:
```
rate(node_cpu_seconds_total[5m])
```
Request rate:
```
rate(http_requests_total[1m])
```
Error rate:
```
sum(rate(http_requests_total{status="500"}[5m]))
```
PromQL is insanely powerful once you get used to it.
#### 6. Alerting
Prometheus can fire alerts when conditions are met.
Example:
```
- alert: HighCPUUsage
  expr: cpu_usage > 0.9
  for: 5m
  labels:
    severity: critical
```
Alerts go to Alertmanager, which can:
* group alerts
* silence alerts
* send notifications to Slack, email, PagerDuty, etc.
### 7. Visualization (Grafana)
Prometheus has a basic UI, but most people use Grafana.

Grafana:
* connects to Prometheus
* visualizes metrics as dashboards
* supports alerts & annotations

This is where monitoring becomes human-friendly.
## How Prometheus is used in real life
**Example: monitoring a Linux server**
* Install node_exporter on the server
* Configure Prometheus to scrape it
* View CPU, memory, disk graphs in Grafana
* Get alerts if disk is almost full
**Example: monitoring an application**
* Add a Prometheus client library to your app (Java, Go, Python, etc.)
* Expose `/metrics`
* Prometheus scrapes it
* Track:
  * request count
  * latency
  * error rates
**Example: Kubernetes**
Prometheus:
* auto-discovers pods and services
* scrapes metrics dynamically
* becomes the backbone of cluster observability
This is the most common Prometheus use case today.
#### What Prometheus is NOT
This part matters.
Prometheus is not:
* a log system (use Loki / ELK)
* a tracing system (use Jaeger / Tempo)
* a long-term data warehouse (without extensions)
Prometheus focuses on fast, reliable metrics.
### When should you use Prometheus?
**Use Prometheus if:**
* You care about system health
* You run microservices or Kubernetes
* You want strong alerting
* You like open-source, cloud-native tools
**Don’t use it alone if:**
* You need years of historical data
* You need logs or traces only
**One-sentence summary**

> **Prometheus monitors systems by regularly pulling numeric metrics, storing them as time-series data, letting you query, visualize, and alert on system health.**

One-sentence summary

Prometheus monitors systems by regularly pulling numeric metrics, storing them as time-series data, letting you query, visualize, and alert on system health.
