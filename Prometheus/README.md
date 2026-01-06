# Prometheus

---

## Table of Contents
- [1. Preparation](#1-preparation)
- [2. Installation](#2-installation)
- [3. Configuration](#3-configuration)
- [4. Enable and Start Prometheus](#4-enable-and-start-prometheus)
- [5. Firewall Configuration](#5-firewall-configuration)
- [6. Access to Web Interface](#6-access-to-web-interface)
- [7. Execute a Query](#7-execute-a-query)
- [8. Node Exporter](#8-node-exporter)
- [9. References](9-references)

---

## 1. Preparation
Update your system

```
sudo apt update && sudo apt upgrade -y
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20140912.png)

Create a `prometheus` user.

```
sudo useradd --no-create-home --shell /bin/false prometheus
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20141044.png)

With the `cat /etc/passwd` command, you can check the creation of the Prometheus user.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20141123.png)

Create two directories (/etc/prometheus and /var/lib/prometheus).

```
sudo mkdir /etc/prometheus
```

```
sudo mkdir /var/lib/prometheus
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20141350.png)

---

## 2. Installation
In the `/tmp` directory, download the latest version of Prometheus with the `wget` command.

> In my case, it was 3.4.

```
cd /tmp
```

```
wget https://github.com/prometheus/prometheus/releases/download/v3.4.0/prometheus-3.4.0.linux-amd64.tar.gz
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20144348.png)

Extract the downloaded package file with the `tar` command. Then, access the directory created as a result of the archive decompression.

```
tar xvf prometheus-3.4.0.linux-amd64.tar.gz
```

```
cd prometheus-3.4.0.linux-amd64
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20144448.png)

Move binaries and set permissions:

```
sudo mv prometheus promtool /usr/local/bin/
```

```
sudo mv prometheus.yml /etc/prometheus/
```

```
sudo chown -R prometheus:prometheus /usr/local/bin/prometheus /usr/local/bin/promtool /etc/prometheus /var/lib/prometheus
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20144554.png)

---

## 3. Configuration
Create a file for Prometheus' systemd service in the `/etc/systemd/system/`.

```
sudo nano /etc/systemd/system/prometheus.service
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20144643.png)

Paste the following contents in the file, then save and exit the file.

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
  --config.file /etc/prometheus/prometheus.yml \
  --storage.tsdb.path /var/lib/prometheus/ \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20144717.png)

Prometheus' configuration file (`prometheus.yml`) can be found at the `/etc/prometheus/` directory.

```
cd /etc/prometheus/
```

```
ls
```

```
sudo vim prometheus.yml
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20145350.png)

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20145452.png)

After saving and exiting the file, you can reload the changes with the following command:

```
sudo systemctl reload prometheus
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20145514.png)

---

## 4. Enable and Start Prometheus
Restart the `systemd` service.

```
sudo systemctl daemon-reload
```

Enable the Prometheus service on boot.

```
sudo systemctl enable --now prometheus
```

Check the status of the Prometheus service.

```
sudo systemctl status prometheus
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20144827.png)

---

## 5. Firewall Configuration
If your ufw firewall is enabled, allow port 9090 and reload your ufw firewall rules.

> If it's not enabled, run the `sudo ufw enable` command.

```
sudo ufw allow 9090/tcp
```

```
sudo ufw reload
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20145010.png)

---

## 6. Access to Web Interface
To access Prometheus' web interface, open a web browser and go to `http://<your_prometheus_ip_address>:9090`.

*Replace the `<your_prometheus_ip_address>` variable with the actual IP address.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20145200.png)

> If your Prometheus service is installed locally on your system (for example, I installed Prometheus on an Ubuntu 22.04 Desktop VM, so I could access Prometheus' web interface on a web browser).

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-05%20194931.png)

---

## 7. Execute a Query
By entering and executing a query, you can monitor any metric you want of your monitored host.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20145644.png)

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-04%20145707.png)

---

## 8. Node Exporter
Create a new user for Prometheus' node exporter.

```
sudo useradd --no-create-home --shell /bin/false node_exporter
```

With the `cat /etc/passwd` command, you can check the creation of the Prometheus Node Exporter user.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-05%20201356.png)

Download the Prometheus Node Exporter with `wget`.

```
wget https://github.com/prometheus/node_exporter/releases/download/v0.16.0/node_exporter-0.16.0.linux-amd64.tar.gz
```

Extract the downloaded archive with `tar`.

```
tar xvf node_exporter-0.16.0.linux-amd64.tar.gz
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-05%20201626.png)

Copy the binary file into the `/usr/local/bin` directory.

```
sudo cp node_exporter-0.16.0.linux-amd64/node_exporter /usr/local/bin
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-05%20201744.png)

Set the ownership to the Prometheus Node Exporter user you created earlier.

```
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-05%20201814.png)

Remove the unnecessary extra files of the Prometheus Node Exporter.

```
rm -rf node_exporter-0.16.0.linux-amd64.tar.gz node_exporter-0.16.0.linux-amd64
```

Create a systemd service file for Prometheus Node Exporter in the `/etc/systemd/system/`.

```
sudo nano /etc/systemd/system/prometheus.service
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-05%20202029.png)

Paste the following contents in the file, then save and exit the file.

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

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-05%20202134.png)

## 4. Enable and Start Prometheus
Restart the `systemd` service.

```
sudo systemctl daemon-reload
```

Enable the Node Exporter service on boot.

```
sudo systemctl enable node_exporter
```

Start the Node Exporter service.

```
sudo systemctl start node_exporter
```

Check the status of the Node Exporter service.

```
sudo systemctl status node_exporter
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Prometheus/Assets/Screenshot%202026-01-05%20202329.png)

---

## 9. References
- [Prometheus - Official Website](https://prometheus.io/)
- [CrownCloud Wiki - Install Prometheus on Ubuntu 22.04 Tutorial](https://wiki.crowncloud.net/How_to_Install_Lets_Encrypt_SSL_Certificate_with_Nginx_on_Ubuntu_20_04?How_to_Install_Prometheus_on_Ubuntu_22_04)
- [CrownCloud Wiki - Configure Prometheus with Grafana](https://wiki.crowncloud.net/?How_to_Configure_Prometheus_Monitoring_Server_with_Grafana_on_Ubuntu_22_04)
