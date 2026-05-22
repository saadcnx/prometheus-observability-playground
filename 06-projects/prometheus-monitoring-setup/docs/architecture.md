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
