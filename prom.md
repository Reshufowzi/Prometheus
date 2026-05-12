```
cd /opt
```
```
wget https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-2.52.0.linux-amd64.tar.gz
```
```
tar -xvf prometheus-2.52.0.linux-amd64.tar.gz
```
```
cd prometheus-2.52.0.linux-amd64
```
```
./Prometheus
```
# http://<EC2-PUBLIC-IP>:9090

```
./prometheus --config.file=prometheus.yml
```
```
cd /opt
```
```
wget https://github.com/prometheus/node_exporter/releases/download/v1.10.2/node_exporter-1.10.2.linux-amd64.tar.gz
```
```
tar xvf node_exporter-1.10.2.linux-amd64.tar.gz
```
```
sudo mv node_exporter-1.8.0.linux-amd64 node_exporter
```
```
cd /opt/node_exporter
```
```
./node_exporter
```
# Listening on :9100
# http://EC2_PUBLIC_IP:9100/metrics
# Now connect Prometheus to Node Exporter
```
cd /opt/prometheus
```
```
nano prometheus.yml
```
```
scrape_configs:
  - job_name: "node_exporter"
    static_configs:
      - targets: ["localhost:9100"]
Prometheus UI → Status → Targets → should show:
node_exporter   UP

```
