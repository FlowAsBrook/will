---
title: Prometheus & Grafana
date: 2024-10-25 15:02:38
tags: tool
categories: monitor
---

# Prometheus

## node_exporter

- package

  ```shell
  wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
  ```

- prepare user and directory

  ```shell
  sudo groupadd -f dingofs
  sudo useradd -g dingofs dingofs
  sudo mkdir /etc/node_exporter
  sudo chown dingofs:dingofs /etc/node_exporter
  
  sudo cp /path/to/node_exporter-files/node_exporter /usr/bin/
  sudo chown dingofs:dingofs /usr/bin/node_exporter
  ```

- config system service

  ```shell
  sudo vi /usr/lib/systemd/system/node_exporter.service
  sudo chmod 664 /usr/lib/systemd/system/node_exporter.service
  ```

  ```yaml
  [Unit]
  Description=Node Exporter
  Documentation=https://prometheus.io/docs/guides/node-exporter/
  Wants=network-online.target
  After=network-online.target
  
  [Service]
  User=dingofs
  Group=dingofs
  Type=simple
  Restart=on-failure
  ExecStart=/usr/bin/node_exporter \
    --web.listen-address=:19100
  
  [Install]
  WantedBy=multi-user.target
  ```

- start system service

  ```shell
  sudo systemctl daemon-reload
  sudo systemctl start node_exporter
  sudo systemctl status node_exporter
  sudo systemctl enable node_exporter.service # Configure node_exporter to start at boot
  ```

## prometheus server

- prometheus.yml

  ```yaml
  global:
    scrape_interval:     15s # By default, scrape targets every 15 seconds.
    evaluation_interval: 15s # Evaluate rules every 15 seconds.
  
    # Attach these extra labels to all timeseries collected by this Prometheus instance.
    external_labels:
      monitor: 'codelab-monitor'
  
  #rule_files:
  #- 'prometheus.rules.yml'
  
  scrape_configs:
    - job_name: 'prometheus'
  
      # Override the global default and scrape targets from this job every 5 seconds.
      scrape_interval: 5s
  
      static_configs:
        - targets: ['172.20.7.232:19090']
  
    - job_name: 'node'
  
      # Override the global default and scrape targets from this job every 5 seconds.
      scrape_interval: 5s
  
      static_configs:
        - targets: ['172.20.7.232:19100','172.20.7.233:19100','172.20.7.234:19100']
          labels:
            group: 'metric1'
  ```

- docker container

```shell
# Create persistent volume for your data
docker volume create prometheus-data
# Start Prometheus container
docker run -d \
    -p 19090:19090 \
    -v /path/to/prometheus.yml:/etc/prometheus/prometheus.yml \
    -v prometheus-data:/prometheus \
    prom/prometheus
```

# Grafana

- deploy container

  ```shell
  # create a persistent volume for your data
  docker volume create grafana-storage
  # verify that the volume was created correctly
  # you should see some JSON output
  docker volume inspect grafana-storage
  
  # start grafana
  docker run -d -p 13000:3000 --name=grafana \
    --volume grafana-storage:/var/lib/grafana \
    grafana/grafana
  
  # web login
  admin/admin
  ```

## dashboard

https://grafana.com/grafana/dashboards/

**just import from existed dashboard json**

enjoy it

- reference
  - [official deploy](https://grafana.com/docs/grafana/latest/setup-grafana/installation/docker/)
  - [config prometheous](https://grafana.com/docs/grafana/latest/datasources/prometheus/configure-prometheus-data-source/)
  - [Get started with Grafana and Prometheus](https://grafana.com/docs/grafana/latest/getting-started/get-started-grafana-prometheus/)
