# TASK13_(Monitoring)
Introduction to server monitoring using prometheus and grafana.

When a system runs, it constantly emits signals about what it’s doing.
The three main observability signals are:
1. **Metrics**
2. **Logs**
3. **Traces**

---

# Prometheus
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
* Logs capture:
* Errors
* Warnings
* User actions
* Stack traces
 Security events

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
