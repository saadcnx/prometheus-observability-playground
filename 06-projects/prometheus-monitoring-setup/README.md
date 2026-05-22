# 🎯 Prometheus Monitoring Setup

> **Production-grade system monitoring setup** using Prometheus & Node Exporter on Linux.
> Deployed on bare-metal cloud instance with systemd service management.

[![Linux](https://img.shields.io/badge/Linux-Ubuntu%2FDebian-orange)](https://ubuntu.com)
[![Prometheus](https://img.shields.io/badge/Prometheus-v2.47.0-e6522c)](https://prometheus.io)
[![Node Exporter](https://img.shields.io/badge/Node%20Exporter-v1.6.1-green)](https://github.com/prometheus/node_exporter)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────┐
│           Prometheus Server              │
│         (Port 9090) — Manager            │
│  ┌─────────────┐    ┌─────────────────┐  │
│  │  TSDB Store │◄───│  Scrape Targets │  │
│  │ /var/lib/.. │    │  every 15s      │  │
│  └─────────────┘    └─────────────────┘  │
└──────────────────┬──────────────────────┘
                   │ scrapes metrics
                   ▼
┌─────────────────────────────────────────┐
│           Node Exporter                  │
│        (Port 9100) — System Guard        │
│                                          │
│  CPU Metrics  •  Memory Metrics          │
│  Disk I/O     •  Network Stats           │
│  System Load  •  File Descriptors        │
└─────────────────────────────────────────┘
```

---

## 🚀 What I Built

| Component | Purpose | Status |
|-----------|---------|:------:|
| Prometheus Server v2.47.0 | Metrics collection & storage | ✅ Active |
| Node Exporter v1.6.1 | Linux system metrics exposure | ✅ Active |
| Systemd Services | Auto-start & process management | ✅ Enabled |
| PromQL Queries | Data analysis & alerting | ✅ Working |
| TSDB Storage | Time-series data persistence | ✅ Configured |

---

## 🛠️ Tech Stack

| Category | Tool |
|----------|------|
| **OS** | Linux (Ubuntu 20.04+ / Debian 11+) |
| **Monitoring** | Prometheus 2.47.0 |
| **Metrics Collection** | Node Exporter 1.6.1 |
| **Service Management** | systemd |
| **Query Language** | PromQL |
| **Storage** | Prometheus TSDB |

---

## 📋 Prerequisites

- Linux-based OS (Ubuntu 20.04+ / Debian 11+)
- `sudo` privileges
- Internet connectivity
- Basic knowledge of YAML & systemd

---

## ⚡ Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/saadcnx/prometheus-monitoring-setup.git
cd prometheus-monitoring-setup
chmod +x scripts/*.sh
```

### 2. Install Prometheus

```bash
sudo ./scripts/install-prometheus.sh
```

### 3. Install Node Exporter

```bash
sudo ./scripts/install-node-exporter.sh
```

### 4. Start & Enable Services

```bash
sudo systemctl start prometheus node_exporter
sudo systemctl enable prometheus node_exporter
```

---

## ✅ Verification

### Check Service Status

```bash
sudo systemctl status prometheus
sudo systemctl status node_exporter
```

### Verify Targets Are UP

```bash
curl http://localhost:9090/api/v1/targets
```

Open browser → `http://localhost:9090/targets` — both targets should show **UP** (green).

### Query Metrics with PromQL

```bash
# Are all targets alive?
curl -G http://localhost:9090/api/v1/query \
  --data-urlencode 'query=up'

# CPU usage (%)
curl -G http://localhost:9090/api/v1/query \
  --data-urlencode 'query=100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)'

# Available memory
curl -G http://localhost:9090/api/v1/query \
  --data-urlencode 'query=node_memory_MemAvailable_bytes'
```

---

## 📈 Useful PromQL Queries

| Query | Description |
|-------|-------------|
| `up` | Target health — `1` = UP, `0` = DOWN |
| `node_cpu_seconds_total` | CPU time breakdown by mode |
| `node_memory_MemAvailable_bytes` | Available RAM in bytes |
| `node_disk_io_time_seconds_total` | Disk I/O utilization |
| `rate(node_network_receive_bytes_total[5m])` | Network receive rate |
| `100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)` | CPU usage % |

---

## 🧯 Troubleshooting

| Problem | Diagnostic Command | Fix |
|---------|--------------------|-----|
| Service won't start | `sudo journalctl -u prometheus -f` | Check logs for errors |
| Config syntax error | `promtool check config /etc/prometheus/prometheus.yml` | Fix YAML formatting |
| Target shows DOWN | `sudo systemctl status node_exporter` | Restart the service |
| Permission denied | `ls -la /var/lib/prometheus/` | Fix directory ownership |

---

## 🎓 What I Learned

- **Prometheus Architecture** — Server, Exporters, TSDB, PromQL
- **Linux System Administration** — Users, permissions, systemd
- **YAML Configuration** — Structured config management
- **Metrics Collection** — Pull-based monitoring model
- **Time-Series Data** — Storage & retrieval patterns
- **Production Deployment** — Service management & auto-start

---

## 📁 Repository Structure

```
prometheus-monitoring-lab/
├── README.md
├── configs/
│   ├── prometheus.yml
│   ├── prometheus.service
│   └── node_exporter.service
├── scripts/
│   ├── install-prometheus.sh
│   └── install-node-exporter.sh
├── docs/
│   ├── architecture.md
│   └── troubleshooting.md
└── LICENSE
```

---

## 📄 License

This project is open source and available under the [MIT License](./LICENSE).
