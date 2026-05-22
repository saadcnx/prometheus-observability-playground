<div align="center">

# 🔥 Prometheus-Observability-Playground

![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Status](https://img.shields.io/badge/Status-Active%20Learning-brightgreen?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

> A structured, hands-on repository documenting my complete Prometheus learning journey —  
> from core concepts to real-world projects.

</div>

---

## 📌 About This Repo

This repository is my personal playground space for **Prometheus** — the open-source monitoring and alerting toolkit. Here you'll find:

- 📚 **Notes & Concepts** — everything I learn, explained simply
- 🛠️ **Projects** — focused hands-on implementations
- ⚙️ **Config Examples** — real `prometheus.yml` and rule files
- 📊 **Dashboards & Queries** — PromQL experiments and Grafana setups

> 💡 **Big / production-level projects** are maintained in their **own separate repositories** and linked here for reference.

---

## 📁 Repository Structure

```
prometheus-learning/
│
├── 📂 01-basics/
│   ├── notes.md                  # Core concepts & architecture
│   ├── installation.md           # Setup on Linux / Docker
│   └── first-scrape/             # First working scrape config
│
├── 📂 02-promql/
│   ├── notes.md                  # PromQL syntax & functions
│   ├── queries.md                # Query examples & results
│   └── exercises/                # Practice queries
│
├── 📂 03-exporters/
│   ├── node-exporter/            # System metrics
│   ├── blackbox-exporter/        # Endpoint probing
│   └── custom-exporter/          # Writing your own exporter
│
├── 📂 04-alerting/
│   ├── notes.md                  # Alertmanager concepts
│   ├── alertmanager.yml          # Config examples
│   └── alert-rules/              # Custom alert rules
│
├── 📂 05-grafana/
│   ├── setup.md                  # Connecting Grafana to Prometheus
│   └── dashboards/               # JSON dashboard exports
│
├── 📂 06-projects/
│   ├── prometheus-monitoring-setup        # setup prometheus
│  
│   
│
└── 📂 resources/
    ├── cheatsheet.md             # Quick reference
    └── links.md                  # Useful docs & tutorials
```

---

## 🧠 What I'm Learning

| Topic | Status |
|-------|--------|
| Prometheus Architecture | ✅ Done |
| Installation & Setup | ✅ Done |
| `prometheus.yml` Configuration | 🔄 In Progress |
| PromQL Basics | 🔄 In Progress |
| Node Exporter | ⏳ Upcoming |
| Alertmanager Setup | ⏳ Upcoming |
| Grafana Integration | ⏳ Upcoming |
| Writing Custom Exporters | ⏳ Upcoming |
| Service Discovery | ⏳ Upcoming |
| Production Best Practices | ⏳ Upcoming |

---

## 🛠️ Projects

| # | Project | Description | Status |
|---|---------|-------------|--------|
| 01 | [Monitor Flask App](./06-mini-projects/monitor-flask-app/) | Expose and scrape metrics from a Python Flask application | ⏳ |
| 02 | [Docker Monitoring](./06-mini-projects/docker-monitoring/) | Monitor running Docker containers using cAdvisor | ⏳ |
| 03 | [Linux System Monitor](./06-mini-projects/linux-system-monitor/) | Full stack: Node Exporter + Prometheus + Grafana | ⏳ |

> 🚀 **Bigger projects** get their own dedicated repos. Links will be added here as they go live.

---

## 🔗 Big Projects (Separate Repos)

| Project | Repo Link | Description |
|---------|-----------|-------------|
| *(Coming Soon)* | — | Will be linked here when live |

---

## ⚙️ Prerequisites

To run the projects in this repo, you'll need:

- **Docker** & **Docker Compose**
- **Prometheus** (v2.x+)
- **Grafana** (optional, for dashboards)
- Basic understanding of **YAML** and **Linux**

---

## 🚀 Quick Start

```bash
# Clone the repo
git clone https://github.com/saadcnx/prometheus-observability-playground.git
cd prometheus-observability-playground

# Jump into any project folder and follow its README
cd 06-projects/monitor-flask-app
```

Each folder has its own `README.md` with step-by-step instructions.

---

## 📖 Resources I'm Using

- [Prometheus Official Docs](https://prometheus.io/docs/)
- [PromQL Cheat Sheet](https://promlabs.com/promql-cheat-sheet/)
- [Grafana + Prometheus Tutorial](https://grafana.com/docs/grafana/latest/getting-started/get-started-grafana-prometheus/)
- [Awesome Prometheus](https://github.com/roaldnefs/awesome-prometheus)

---

## 📝 Notes Style

All notes in this repo follow this format:

```
## Topic Name
- What it is
- Why it matters
- How it works (with examples)
- My observations / gotchas
```

---

## 🤝 Contributing

This is a personal learning repo, but if you spot an error or want to suggest something feel free to open an issue!

---

<div align="center">

⭐ Star this repo if it helps you too!

</div>
