# Prometheus
### 1. Architecture Overview
```
Node Exporter → Prometheus → Alertmanager → Slack
                         ↓
                      Grafana

```
### 1. Server Prerequisites

### Use Ubuntu (EC2 / VM / local)

sudo apt update && sudo apt upgrade -y

### Install basic tools:

sudo apt install -y curl wget git

```
```
### 2. Install Docker
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker

```

```
### Add user to Docker group (IMPORTANT)
sudo usermod -aG docker $USER
newgrp docker

```

```
### 3. Install Docker Compose
sudo apt install -y docker-compose
```
```
### 4. Create Project Structure
mkdir monitoring-stack
cd monitoring-stack

mkdir prometheus alertmanager grafana
```

### 5. Prometheus Setup
📄 prometheus.yml
```
nano prometheus/prometheus.yml
```

```
global:
  scrape_interval: 15s

rule_files:
  - /etc/prometheus/alert.rules.yml

alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - alertmanager:9093

scrape_configs:

  - job_name: "node-exporter"
    static_configs:
      - targets: ["node-exporter:9100"]
   
```

## alert.rules.yml
```
nano prometheus/alert.rules.yml

```
groups:
  - name: node-alerts
    rules:

      - alert: HighCPUUsage
        expr: 100 - (avg by(instance)(irate(node_cpu_seconds_total{mode="idle"}[1m])) * 100) > 80
        for: 1m
        labels:
          severity: warning
        annotations:
          summary: "High CPU Usage detected"

      - alert: NodeDown
        expr: up == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Node is down"
```
Alertmanager Setup
```

```
global:
  resolve_timeout: 5m

route:
  receiver: slack-alerts

receivers:
  - name: slack-alerts
    slack_configs:
      - api_url: 'https://hooks.slack.com/services/YOUR/WEBHOOK/URL'
        channel: '#alerts'
        send_resolved: true
```

```
7. Slack Webhook Setup
Go to Slack → Create channel #alerts
Visit: https://api.slack.com/apps
Create App → Enable Incoming Webhooks
Generate webhook URL
Replace in alertmanager.yml

```
### 8. Docker Compose File

```
nano docker-compose.yml
```
```
version: "3.8"

services:

  prometheus:
    image: prom/prometheus
    container_name: prometheus
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - ./prometheus/alert.rules.yml:/etc/prometheus/alert.rules.yml
    ports:
      - "9090:9090"
    depends_on:
      - node-exporter

  node-exporter:
    image: prom/node-exporter
    container_name: node-exporter
    ports:
      - "9100:9100"

  alertmanager:
    image: prom/alertmanager
    container_name: alertmanager
    volumes:
      - ./alertmanager/alertmanager.yml:/etc/alertmanager/alertmanager.yml
    ports:
      - "9093:9093"

  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - "3000:3000"
```
### 9. Start the Stack
```
docker-compose up -d
```
```
10. Access Applications
Prometheus → http://<YOUR-IP>:9090
Node Exporter → http://<YOUR-IP>:9100/metrics
Alertmanager → http://<YOUR-IP>:9093
Grafana → http://<YOUR-IP>:3000
```
```
11. Configure Grafana

Login:

user: admin
password: admin
```
```
Add Data Source
Type: Prometheus

URL:

http://prometheus:9090
➤ Import Dashboard
Go to → Import
Enter ID: 1860 (Node Exporter Dashboard)
🧪 12. Test Alert

Run CPU stress:

yes > /dev/null &

After ~1 minute:

✅ Alert triggers
✅ Alertmanager fires
✅ Slack receives message

🛑 13. Stop Load Test
killall yes
🔍 14. Verify Everything
Prometheus Targets:
http://<IP>:9090/targets

All should be UP

⚠️ Common Problems
❌ Node exporter DOWN
Check:
docker logs node-exporter
```
