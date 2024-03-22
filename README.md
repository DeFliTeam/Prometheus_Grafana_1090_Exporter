# Prometheus_Grafana_1090_Exporter 

This is a test installation for exporting tar1090 metrics via prometheus in to Grafana. A single install will be available soon 

**Exporter Install** 

```bash
pip3 install dump1090exporter
```

Run the following command

```bash

   dump1090exporter \
  --resource-path=http://localhost/tar1090/data/ \
  --port=9105 \
  
```

**Docker** 

```bash
$ docker run -it --rm clawsicus/dump1090exporter
usage: dump1090-exporter [-h] [--url <dump1090 url>]
...

```bash
$ docker run -p 9105:9105 \
  --detach \
  --restart always \
  clawsicus/dump1090exporter \
  --url=http://localhost:8080
```
  








**Prometheus Install**

```bash
sudo useradd -M -r -s /bin/false prometheus
```
```bash
sudo mkdir /etc/prometheus /var/lib/prometheus
```

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.51.0/prometheus-2.51.0.linux-amd64.tar.gz
```

```bash
tar xzf prometheus-2.51.0.linux-amd64.tar.gz prometheus-2.51.0.linux-amd64/
```

```bash
sudo cp prometheus-2.51.0.linux-amd64/{prometheus,promtool} /usr/local/bin/
```

```bash
sudo chown prometheus:prometheus /usr/local/bin/{prometheus,promtool}
```

```bash
sudo cp -r prometheus-2.51.0.linux-amd64/{consoles,console_libraries} /etc/prometheus/
``` 

```bash
sudo cp prometheus-2.51.0.linux-amd64/prometheus.yml /etc/prometheus/prometheus.yml
``` 

```bash
sudo chown -R prometheus:prometheus /etc/prometheus
```

```bash
sudo chown prometheus:prometheus /var/lib/prometheus
```

```bash
sudo nano /etc/systemd/system/prometheus.service  
```
**PASTE**

```bash
[Unit] 
Description Prometheus Server 
Wants=network-online.target 
After=network-online.target 

[Service] 
User=prometheus 
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \ 
 --config.file=/etc/prometheus/prometheus.yml \ 
 --storage.tsdb.path=/var/lib/prometheus/ \ 
 --web.console.templates=/etc/prometheus/consoles \ 
 --web.console.libraries=/etc/prometheus/console_libraries 
 
 [Install] 
 WantedBy=multi-user.target
```

 ```bash
 ctrl x + y
 ```

 ```bash
 sudo nano /etc/prometheus/prometheus.yml
```
 
 **Use the following data inputs (note you must change your job name to your bucket where specified)** 

```bash
 #my global config
 global:
  scrape_interval: 10s
  evaluation_interval: 10s
  external_labels:
    origin_prometheus: prometheus01
remote_write:
  - url: https://prometheus-prod-13-prod-us-east-0.grafana.net/api/prom/push
    basic_auth:
      username: 1463871
      password: glc_eyJvIjoiMTA3MTQxOSIsIm4iOiJzdGFjay04NzU2NzItaG0tcmVhZC1uZXdfZGVmbGkiLCJrIjoiQzd4dDFCNEtRN2o3QTJnSjZ1bTYxMEtKIiwibSI6eyJyIjoicHJvZC11cy1lYXN0LTAifX0=

# Alertmanager configuration
alerting:
  alertmanagers:
   - static_configs:
       - targets:
         # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

scrape_configs:
  - job_name: Your_bucket_name
    static_configs:
      - targets: ["localhost:9015","localhost:9090"]
 
```

 ```bash     
 ctl x + y
```

```bash
 sudo systemctl daemon-reload
```

 ```bash
 sudo systemctl start prometheus
```

```bash
 sudo systemctl enable prometheus
```

```bash
sudo systemctl restart prometheus
 ```

**Trouble Shooting** 

You can change the "localhost" parameters to your local IP address (192.xxx.xxx.xxx) or alternatively to the public IP address where you find your tar1090
