Setting up Grafana-Prometheus for watching ubuntu server (metrics )

STeps i did : PROMETHEUS Creating users and direcotry sudo useradd --no-create-home --shell /bin/false prometheus sudo mkdir /etc/prometheus sudo mkdir /var/lib/prometheus sudo chown prometheus:prometheus /etc/prometheus /var/lib/prometheus
Downloadin and getting Prometheus cd /tmp wget https://github.com/prometheus/prometheus/releases/download/v2.48.0/prometheus-2.48.0.linux-amd64.tar.gz tar xvf prometheus-.linux-amd64.tar.gz cd prometheus-.linux-amd64 sudo mv prometheus promtool /usr/local/bin/ sudo chown prometheus:prometheus /usr/local/bin/prometheus /usr/local/bin/promtool sudo mv consoles console_libraries /etc/prometheus/ sudo mv prometheus.yml /etc/prometheus/ sudo chown -R prometheus:prometheus /etc/prometheus/
Creating a systemd for prometheus sudo tee /etc/systemd/system/prometheus.service > /dev/null <<EOF [Unit] Description=Prometheus Wants=network-online.target After=network-online.target
[Service] User=prometheus Group=prometheus Type=simple ExecStart=/usr/local/bin/prometheus
--config.file=/etc/prometheus/prometheus.yml
--storage.tsdb.path=/var/lib/prometheus
--web.listen-address=0.0.0.0:9090
--storage.tsdb.retention.time=15d
--log.level=info
[Install] WantedBy=multi-user.target EOF


Then i did reload systemd and prometheus sudo systemctl daemon-reload sudo systemctl enable --now prometheus
then i install grafana-server and adding grafana repo and enebaling the service sudo systemctl daemon-reload sudo systemctl enable --now prometheus

INSTALL GRAFANA sudo apt update && sudo apt install -y apt-transport-https software-properties-common sudo mkdir -p /etc/apt/keyrings curl -fsSL https://packages.grafana.com/gpg.key | sudo tee /etc/apt/keyrings/grafana.asc
echo "deb [signed-by=/etc/apt/keyrings/grafana.asc] https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list sudo apt update && sudo apt install -y grafana
sudo systemctl enable --now grafana-server
sudo systemctl start grafana-server and check if it is active
sudo systemctl status grafana-server 

![image](https://github.com/user-attachments/assets/8194e941-5c12-492f-aab9-4eff99dbb45c)

Here is how need's to be : 

![image](https://github.com/user-attachments/assets/d9072d37-2581-4193-80bd-541b186c3675)

