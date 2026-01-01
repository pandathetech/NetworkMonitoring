# Nessus Guide on Ubuntu 22.04
My Nessus guide provides step-by-step instructions for installing Nessus version 10.7.1 on an Ubuntu 22.04 system.

---

## Table of Contents
- [1. Update the System](#1-update-the-system)
- [2. Install Required Packages](#2-install-required-packages)
- [3. Download Nessus](#3-download-nessus)
- [4. Install Nessus](#4-install-nessus)
- [5. Enable and Start Nessus](#5-enable-and-start-nessus)
- [6. Configure the Firewall](#6-configure-the-firewall)
- [7. Access the Nessus Web Interface](#7-access-the-nessus-web-interface)

---

## 1. Update the System
Update the system package list and apply available upgrades:

```
sudo apt update && sudo apt full-upgrade -y
```

---

## 2. Install Required Packages
Install curl for downloading files:

```
sudo apt install curl -y
```

Install OpenSSH client and server:

```
sudo apt install openssh-client
sudo apt install openssh-server -y
```

---

## 3. Download Nessus
Download the Nessus .deb package directly from Tenable:

```
curl --request GET \
  --url 'https://www.tenable.com/downloads/api/v2/pages/nessus/files/Nessus-10.7.1-ubuntu1404_amd64.deb' \
  --output 'Nessus-10.7.1-ubuntu1404_amd64.deb
```

---

## 4. Install Nessus
Use dpkg to install the Nessus package:

```
sudo dpkg -i Nessus-10.7.1-ubuntu1404_amd64.deb
```

---

## 5. Enable and Start Nessus
Enable and start the Nessus daemon:

```
sudo systemctl enable nessusd.service
sudo systemctl start nessusd.service
```

---

## 6. Configure the Firewall
Allow Nessus and OpenSSH traffic through the UFW firewall:

```
sudo ufw allow "OpenSSH"
sudo ufw allow 8834/tcp
sudo ufw enable
```

---

## 7. Access the Nessus Web Interface
Once Nessus is installed and running, open a web browser and go to `https://localhost:8834`. This will open the Nessus web interface where you can complete the setup and start vulnerability scans.
