# Prometheus
### 1. Architecture Overview
```
Node Exporter → Prometheus → Alertmanager → Slack
                         ↓
                      Grafana

```
1. Server Prerequisites

Use Ubuntu (EC2 / VM / local)

sudo apt update && sudo apt upgrade -y

Install basic tools:

sudo apt install -y curl wget git

```
