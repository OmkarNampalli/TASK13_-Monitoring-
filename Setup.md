# Installing Prometheus
## Step 1
* download latest binary of prometheus from official website and unzip it:
  * For linux use following command: `wget <prometheus-link>`
  * to unzip or untar it use: `tar -xvf prometheus-*.tar.gz`
## Step 2
* Create a user named prometheus; This user should not be able to `log in` to system or use the `shell`.
> it is done for security and isolation.
* use command:
  ```
  sudo useradd --no-create-home --shell /bin/false prometheus
  ```
  ## Step 3
  * Copy related files of prometheus into their specific directory
    * Config files such as `prometheus.yml` into `/etc`
    * Create data file such as `/var/lib/prometheus`
    * use command:
      ```
      sudo mkdir -p /etc/prometheus
      sudo mkdir -p /var/lib/prometheus
      sudo mv prometheus.yml /etc/prometheus/
      ```
    * binary files in system path `/usr/local/bin`
      command:
      ```
      sudo mv prometheus promtool /usr/local/bin/
      ```
  ## Step 4
  * Run prometheus as systemd service
    * change the ownership of prometheus files
      ```
      sudo mv prometheus.yml /etc/prometheus/
      ```
    * Create the service file
      ```
      sudo vi /etc/systemd/system/prometheus.service
      ```
    * Paste the following content
      ```
      [Unit]
      Description=Prometheus
      Wants=network-online.target
      After=network-online.target
      
      [Service]
      User=prometheus
      Group=prometheus
      Type=simple
      ExecStart=/usr/local/bin/prometheus \
        --config.file=/etc/prometheus/prometheus.yml \
        --storage.tsdb.path=/var/lib/prometheus

      [Install]
      WantedBy=multi-user.target
      ```
    * Reload and start
      ```
      sudo systemctl daemon-reexec
      sudo systemctl daemon-reload
      sudo systemctl enable prometheus
      sudo systemctl start prometheus
      ```

---

# Configuring prometheus to scrape system metrics
Prometheus does not scrape metrics automatically it needs a metrics exporter.

In our case we are monitoring our system hence we need **node_exporter**.
* Install `node_exporter`: follow the same process as used for prometheus.
  * the download command `wget https://github.com/prometheus/node_exporter/releases/download/v1.10.2/node_exporter-1.10.2.linux-amd64.tar.gz`
  * the service file
  ```
  [Unit]
  Description=Node Exporter
  Wants=network-online.target
  After=network-online.target
  
  [Service]
  User=node_exporter
  Group=node_exporter
  Type=simple
   ExecStart=/usr/local/bin/node_exporter


  [Install]
  WantedBy=multi-user.target
  ```
* Paste follwing under `scrape_configs`
  * `sudo vi /etc/prometheus/prometheus.yml`
  * ```
    - job_name: "node_exporter"
      static_configs:
        - targets: ["localhost:9100"]
    ```
* Restart prometheus `sudo systemcl restart prometheus`

---

# Installing Grafana
> *Using package manager*

For Amazon Linux / RHEL / CentOS:
1. 
```
sudo tee /etc/yum.repos.d/grafana.repo <<EOF
[grafana]
name=Grafana Repository
baseurl=https://packages.grafana.com/oss/rpm
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://packages.grafana.com/gpg.key
EOF
```
2. 
```
sudo yum install grafana -y
```

For Ubuntu / Debian::
```
sudo apt install grafana -y
```

## Start and enable Grafana
```
sudo systemctl daemon-reload
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
sudo systemctl status grafana-server
```


---


# Configuring Grafana and its Dashboard
- open browser and go to `htttp://localhost:3000`
- login with username `admin` and password `admin`
- add data source and select prometheus
  - url : `http://localhost:9090`
- add dashboard
  - home
  - dashboard
  - new
  - import
  - code : `1860`

  - save and test
