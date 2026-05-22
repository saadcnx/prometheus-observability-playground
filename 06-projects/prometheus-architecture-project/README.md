# 🔥 Prometheus Monitoring Stack

![Prometheus](https://img.shields.io/badge/Prometheus-v2.47.0-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Node Exporter](https://img.shields.io/badge/Node_Exporter-v1.6.1-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Alertmanager](https://img.shields.io/badge/Alertmanager-v0.26.0-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![OS](https://img.shields.io/badge/OS-Ubuntu_24.04-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Shell](https://img.shields.io/badge/Shell-Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

A **production-grade Prometheus monitoring stack** deployed on a bare-metal Ubuntu Linux server. This project covers the full observability pipeline — from metrics collection via exporters, to alerting via Alertmanager, to data querying with PromQL — all managed as systemd services.

> Built as part of a hands-on DevOps lab to demonstrate real-world system monitoring practices used in SRE and cloud operations roles.

---

## 📋 Table of Contents

- [Architecture](#-architecture)
- [Stack Overview](#-stack-overview)
- [Project Structure](#-project-structure)
- [Prerequisites](#-prerequisites)
- [Quick Start](#-quick-start)
- [Component Setup](#-component-setup)
- [Alert Rules](#-alert-rules)
- [PromQL Query Reference](#-promql-query-reference)
- [Verification & Troubleshooting](#-verification--troubleshooting)
- [Screenshots](#-screenshots)
- [Key Learnings](#-key-learnings)

---

## 🏗 Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Linux Server                          │
│                                                          │
│   ┌──────────────┐      ┌──────────────────────────┐    │
│   │ Node Exporter│      │   Process Exporter       │    │
│   │   :9100      │      │       :9256              │    │
│   └──────┬───────┘      └───────────┬──────────────┘    │
│          │    scrape /metrics        │                   │
│          └──────────┬───────────────┘                   │
│                     ▼                                    │
│           ┌─────────────────┐                           │
│           │   Prometheus    │ ◄── prometheus.yml        │
│           │     :9090       │ ◄── alert_rules.yml       │
│           └────────┬────────┘                           │
│                    │  fires alerts                       │
│                    ▼                                     │
│           ┌─────────────────┐                           │
│           │  Alertmanager   │ ◄── alertmanager.yml      │
│           │     :9093       │                           │
│           └─────────────────┘                           │
└─────────────────────────────────────────────────────────┘
```

**Data Flow:**
1. Exporters expose metrics on their respective HTTP endpoints (`/metrics`)
2. Prometheus scrapes all targets every **15 seconds**
3. Alert rules are evaluated every **15 seconds** against collected data
4. When a rule threshold is breached, Prometheus sends the alert to Alertmanager
5. Alertmanager routes and delivers notifications based on configured receivers

---

## 🧰 Stack Overview

| Component | Version | Port | Role |
|---|---|---|---|
| **Prometheus** | v2.47.0 | `:9090` | Central metrics storage & query engine |
| **Node Exporter** | v1.6.1 | `:9100` | Hardware & OS metrics (CPU, RAM, Disk, Network) |
| **Process Exporter** | v0.7.10 | `:9256` | Per-process metrics (CPU, memory, thread count) |
| **Alertmanager** | v0.26.0 | `:9093` | Alert deduplication, routing & notifications |
| **PromTool** | v2.47.0 | CLI | Config validation & rule checking utility |

---

## 📁 Project Structure

```
prometheus-monitoring-stack/
│
├── README.md                        # Project documentation
│
├── configs/                         # All configuration files
│   ├── prometheus.yml               # Scrape targets, evaluation intervals, alerting
│   ├── alert_rules.yml              # Alerting rules (CPU, Memory, Instance Down)
│   ├── alertmanager.yml             # Alert routing and webhook receiver
│   └── process-exporter.yml        # Process name matching config
│
├── services/                        # Systemd service unit files
│   ├── prometheus.service
│   ├── node_exporter.service
│   ├── alertmanager.service
│   └── process_exporter.service
│
├── scripts/
│   ├── install.sh                   # Full one-shot installation script
│   └── cpu_stress.sh                # CPU stress test to trigger alerts
│
└── docs/
    ├── architecture.md              # Detailed component breakdown
    └── screenshots/                 # Prometheus UI screenshots
```

---

## ✅ Prerequisites

- Ubuntu 20.04 / 22.04 / 24.04 (bare metal or VM)
- `sudo` / root access
- Internet access to download binaries from GitHub releases
- Ports `9090`, `9093`, `9100`, `9256` open (check with `sudo ufw status`)

---

## ⚡ Quick Start

Clone the repo and run the automated installer:

```bash
git clone https://github.com/YOUR_USERNAME/prometheus-monitoring-stack.git
cd prometheus-monitoring-stack
chmod +x scripts/install.sh
sudo ./scripts/install.sh
```

Then verify all services are running:

```bash
sudo systemctl status prometheus node_exporter process_exporter alertmanager
```

Access the Prometheus UI at: **`http://<YOUR_SERVER_IP>:9090`**

---

## 🔧 Component Setup

### 1. Prometheus Server

```bash
# Download and install
cd /tmp
wget https://github.com/prometheus/prometheus/releases/download/v2.47.0/prometheus-2.47.0.linux-amd64.tar.gz
tar xvf prometheus-2.47.0.linux-amd64.tar.gz

# Create dedicated system user
sudo useradd --no-create-home --shell /bin/false prometheus

# Copy binaries
sudo cp prometheus-2.47.0.linux-amd64/prometheus /usr/local/bin/
sudo cp prometheus-2.47.0.linux-amd64/promtool /usr/local/bin/
```

The main config file is at `/etc/prometheus/prometheus.yml`. It defines:
- Global scrape/evaluation intervals (15s)
- Alertmanager endpoint (`localhost:9093`)
- Scrape jobs for all three targets

### 2. Node Exporter

Collects system-level metrics: CPU utilization, memory usage, disk I/O, filesystem space, and network throughput.

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
tar xvf node_exporter-1.6.1.linux-amd64.tar.gz
sudo cp node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
```

Verify metrics are being exposed:
```bash
curl http://localhost:9100/metrics | head -20
```

### 3. Process Exporter

Monitors individual Linux processes — useful for tracking resource usage of specific services like `prometheus`, `node_exporter`, `sshd`, etc.

Config at `/etc/process-exporter/config.yml`:
```yaml
process_names:
  - name: "{{.Comm}}"
    cmdline:
      - '.+'
```

### 4. Alertmanager

Handles alert lifecycle: deduplication, grouping, silencing, and routing to receivers (email, Slack, webhook, PagerDuty, etc.).

Config at `/etc/alertmanager/alertmanager.yml`:
```yaml
route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 1h
  receiver: 'web.hook'

receivers:
  - name: 'web.hook'
    webhook_configs:
      - url: 'http://127.0.0.1:5001/'
```

---

## 🚨 Alert Rules

Defined in `configs/alert_rules.yml`. Three rules are active:

### InstanceDown
```yaml
alert: InstanceDown
expr: up == 0
for: 1m
severity: critical
```
Fires when any scrape target has been unreachable for more than **1 minute**.

### HighCPUUsage
```yaml
alert: HighCPUUsage
expr: 100 - (avg by(instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
for: 2m
severity: warning
```
Fires when CPU usage exceeds **80%** for more than **2 minutes**. Uses `irate()` for per-second rate calculation over a 5-minute window.

### HighMemoryUsage
```yaml
alert: HighMemoryUsage
expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100 > 85
for: 2m
severity: warning
```
Fires when memory consumption exceeds **85%** for more than **2 minutes**.

**Test alerts manually:**
```bash
# Trigger CPU stress
chmod +x scripts/cpu_stress.sh
./scripts/cpu_stress.sh

# Wait 2-3 minutes, then check alerts at:
# http://<YOUR_IP>:9090/alerts

# Stop the stress test
killall yes
```

---

## 📊 PromQL Query Reference

Useful queries to run in the Prometheus Graph UI (`/graph`):

```promql
# Check all scrape targets status (1 = up, 0 = down)
up

# Real-time CPU usage percentage per instance
100 - (avg by(instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Memory usage percentage
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100

# Available disk space (excluding tmpfs)
node_filesystem_avail_bytes{fstype!="tmpfs"}

# Inbound network traffic (bytes/sec, 5-min rate)
rate(node_network_receive_bytes_total[5m])

# Outbound network traffic (bytes/sec)
rate(node_network_transmit_bytes_total[5m])

# Number of running processes per instance
namedprocess_namegroup_num_procs
```

---

## 🔍 Verification & Troubleshooting

**Check all services at once:**
```bash
sudo systemctl status prometheus node_exporter process_exporter alertmanager
```

**Verify listening ports:**
```bash
sudo netstat -tlnp | grep -E ':(9090|9100|9256|9093)'
```

**Validate config files before applying:**
```bash
promtool check config /etc/prometheus/prometheus.yml
promtool check rules /etc/prometheus/alert_rules.yml
```

**Check target health via API:**
```bash
curl -s http://localhost:9090/api/v1/targets | jq '.data.activeTargets[] | {job: .labels.job, health: .health}'
```

**View live logs:**
```bash
sudo journalctl -u prometheus -f
sudo journalctl -u node_exporter -f
```

| Issue | Likely Cause | Fix |
|---|---|---|
| Service fails to start | Wrong file permissions | Check `chown` for service user |
| Target shows "down" | Port not listening | Verify service is running on expected port |
| Config parse error | YAML indentation issue | Run `promtool check config` |
| Alert not firing | Threshold not met or wrong `for` duration | Lower threshold temporarily to test |

---

## 📸 Screenshots

> *(Add screenshots of your Prometheus UI here)*

| View | Description |
|---|---|
| `docs/screenshots/targets.png` | All scrape targets healthy (green) |
| `docs/screenshots/graph-cpu.png` | CPU usage PromQL graph |
| `docs/screenshots/alerts.png` | HighCPUUsage alert firing during stress test |
| `docs/screenshots/alertmanager.png` | Alertmanager UI showing active alerts |

---

## 💡 Key Learnings

- **Pull vs Push model:** Prometheus actively scrapes targets instead of waiting for metrics to be pushed, giving it more control over collection intervals and target health tracking.
- **Exporter pattern:** Any service can be made observable by pairing it with an exporter that translates its internal state into the Prometheus exposition format.
- **PromQL power:** The query language allows computing rates, aggregations, and ratios directly at query time — no pre-processing needed.
- **Alertmanager separation of concerns:** Decoupling alert routing from alerting rules means you can change notification channels without touching Prometheus config.
- **systemd integration:** Running each component as a dedicated unprivileged system user following the principle of least privilege is a production best practice.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

## 🙋‍♂️ Author

**Your Name**  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/YOUR_PROFILE)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat&logo=github)](https://github.com/YOUR_USERNAME)

---

*If this project helped you, consider giving it a ⭐ on GitHub!*