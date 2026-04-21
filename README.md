# Enterprise Monitoring Stack: Prometheus & Grafana on Ubuntu

 Project Vision
This repository documents the full-cycle deployment of a professional monitoring ecosystem. By combining **Prometheus** (the database and scraper) with **Grafana** (the visualization engine), we create a powerful observability platform capable of tracking system health, performance metrics, and hardware stability in real-time.

Establishing this stack is a critical step for any IT infrastructure to ensure **High Availability** and **Proactive Maintenance**.

Architecture Overview
* **Prometheus:** Acts as the Time Series Database (TSDB) that pulls metrics from various targets (like Node Exporter or ESXi).
* **Grafana:** Connects to Prometheus as a data source to build intuitive, real-time dashboards.
* **Target:** Ubuntu Server (Metric collection for CPU, Memory, Disk, and Network).


Phase One: Prometheus Installation

Security & Environment Setup
We start by creating a dedicated service user and necessary directories to isolate the process for security reasons.


# Create a system user without home directory or login shell
sudo useradd --no-create-home --shell /bin/false prometheus

# Create configuration and data directories
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus

# Set ownership
sudo chown prometheus:prometheus /etc/prometheus /var/lib/prometheus
3.2 Binary Deployment
Downloading and installing the Prometheus binaries from the official source.

Bash
cd /tmp
wget [https://github.com/prometheus/prometheus/releases/download/v2.48.0/prometheus-2.48.0.linux-amd64.tar.gz](https://github.com/prometheus/prometheus/releases/download/v2.48.0/prometheus-2.48.0.linux-amd64.tar.gz)
tar xvf prometheus-2.48.0.linux-amd64.tar.gz
cd prometheus-2.48.0.linux-amd64

# Move binaries to local path
sudo mv prometheus promtool /usr/local/bin/
sudo chown prometheus:prometheus /usr/local/bin/prometheus /usr/local/bin/promtool

# Move configuration files and libraries
sudo mv consoles console_libraries /etc/prometheus/
sudo mv prometheus.yml /etc/prometheus/
sudo chown -R prometheus:prometheus /etc/prometheus/
3.3 Systemd Service Configuration
Creating a daemon to ensure Prometheus starts automatically on boot.

sudo tee /etc/systemd/system/prometheus.service > /dev/null <<EOF
[Unit]
Description=Prometheus Time Series Database
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \\
  --config.file=/etc/prometheus/prometheus.yml \\
  --storage.tsdb.path=/var/lib/prometheus \\
  --web.listen-address=0.0.0.0:9090 \\
  --storage.tsdb.retention.time=15d \\
  --log.level=info

[Install]
WantedBy=multi-user.target
EOF

Reload and Start
sudo systemctl daemon-reload
sudo systemctl enable --now prometheus
Grafana Installation
Grafana provides the visual layer to our monitoring data.

4.1 Repository Setup & Installation
sudo apt update && sudo apt install -y apt-transport-https software-properties-common
sudo mkdir -p /etc/apt/keyrings
curl -fsSL [https://packages.grafana.com/gpg.key](https://packages.grafana.com/gpg.key) | sudo tee /etc/apt/keyrings/grafana.asc

echo "deb [signed-by=/etc/apt/keyrings/grafana.asc] [https://packages.grafana.com/oss/deb](https://packages.grafana.com/oss/deb) stable main" | sudo tee /etc/apt/sources.list.d/grafana.list

sudo apt update && sudo apt install -y grafana
sudo systemctl enable --now grafana-server
sudo systemctl start grafana-server

# Verify the status
sudo systemctl status grafana-server
Post-Installation Steps
Prometheus UI: Access via http://YOUR_SERVER_IP:9090 to check targets.

Grafana UI: Access via http://YOUR_SERVER_IP:3000 (Default login: admin/admin).

Data Integration: In Grafana, add Prometheus as a data source using the URL http://localhost:9090.

Dashboards: Import popular dashboards (like ID: 1860 for Node Exporter) to see your metrics come to life.

💡 Why This Setup is Critical
This stack isn't just about "seeing charts"—it's about Reliability.

Historical Analysis: Retention for 15 days allows you to spot trends and recurring issues.

Scalability: You can add hundreds of exporters to this single Prometheus instance.

Alerting: Integration with Alertmanager allows for instant notifications via Email, Slack, or PagerDuty.

![image](https://github.com/user-attachments/assets/8194e941-5c12-492f-aab9-4eff99dbb45c)

Here is how need's to be : 

![image](https://github.com/user-attachments/assets/d9072d37-2581-4193-80bd-541b186c3675)

