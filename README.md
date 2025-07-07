# 💻 Zabbix Monitoring Lab - Manual Deployment

This project documents the manual setup of a fully functional **Zabbix 6.0 LTS monitoring server** running on **Ubuntu Server 22.04**, hosted on a **VirtualBox virtual machine**. The lab demonstrates how to configure system monitoring from the ground up, with hands-on work at every stage.

---

## 📌 Overview

This lab simulates a production-style environment for monitoring servers, services, and systems using Zabbix. It includes:

* Manual installation of Ubuntu Server 22.04 (LTS)
* Installation and configuration of Zabbix server, frontend, and agent
* MySQL database setup with security hardening
* Apache web frontend for dashboard access
* Bridged networking for external browser access

---

## 🛠️ System Requirements

* **Host OS**: Windows 10/11
* **Virtualization Tool**: VirtualBox (latest version)
* **ISO Used**: ubuntu-22.04.5-live-server-amd64.iso
* **VM Specs**:

  * 2 GB RAM
  * 1 CPU
  * 30 GB dynamically allocated disk
  * Bridged Network Adapter (Ethernet preferred)

---

## 🔧 Step-by-Step Setup

### 1. Ubuntu Server Installation

* Selected "Ubuntu Server" (not minimized)
* Configured LVM with full disk
* Enabled OpenSSH Server
* Skipped featured snaps (e.g., Docker, Kubernetes)

### 2. Network Configuration

* Bridged Adapter enabled in VirtualBox
* Physical Ethernet used (Wi-Fi bridge may not work reliably)
* Verified IP via `ip a` after reboot

### 3. MySQL Configuration

* Installed MySQL via apt
* Ran `mysql_secure_installation`

  * Removed anonymous users
  * Disallowed root remote login
  * Removed test database
* Logged into MySQL as root and created Zabbix DB:

```sql
CREATE DATABASE zabbix character set utf8mb4 collate utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY '<your-password-here>';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
```

---

## 📦 Zabbix Installation

### Repository & Packages

```bash
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo apt update
```

### Install Zabbix Server, Frontend, and Agent

```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-agent mysql-server -y
```

### Import Initial Database Schema

```bash
zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -u zabbix -p zabbix
```

### Configure Zabbix Server

```bash
sudo nano /etc/zabbix/zabbix_server.conf
# Set:
DBPassword=<your-password-here>
```

### Start and Enable Services

```bash
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2
```

---

## 🌐 Accessing the Dashboard

### Get IP Address

```bash
ip a
```

Use the `inet` value under your bridged interface (e.g., `192.168.x.x` or `10.x.x.x`).

### Open Zabbix UI

On host machine browser:

```
http://<your-vm-ip>/zabbix
```

### Login Credentials

* **Username**: `Admin`
* **Password**: *your chosen Zabbix admin password during setup*

---

## 🧠 Lessons Learned / Troubleshooting

* VirtualBox "Port Forwarding" UI sometimes fails to display — bridged adapter with Ethernet is more reliable
* Cloud-init sometimes traps users in `bash: systemctl: command not found` — switching to manual install avoids this
* `Ctrl + C` and `q` are lifesavers for exiting stuck command prompts or paged output
* Password mismatches and multiline SQL input can cause MySQL errors — be meticulous

---

## 📸 Screenshots to Include

Here are the key screenshots included in this project:

### VirtualBox Network Configuration

![VirtualBox Network](screenshots/virtualbox-network.png)

### Ubuntu Language Selection

![Ubuntu Language](screenshots/ubuntu-language-selection.png)

### Network Configuration During Install

![Network Config](screenshots/ubuntu-network-config.png)

### User and Hostname Setup

![User Setup](screenshots/ubuntu-user-setup.png)

### Storage Configuration (LVM)

![LVM Setup](screenshots/ubuntu-lvm-setup.png)

### OpenSSH Server Enabled During Ubuntu Installation

![OpenSSH Enabled](screenshots/ubuntu-openssh-selected.png)

### Final Install Summary

![Install Summary](screenshots/ubuntu-install-summary.png)

### IP Address Confirmation via `ip a`

![IP Address](screenshots/ip-address-confirmation.png)

### Zabbix Login Page

![Zabbix Login](screenshots/zabbix-login.png)

### Zabbix Dashboard Overview

![Zabbix Dashboard](screenshots/zabbix-dashboard.png)

---

## 🚀 Future Enhancements

* Add monitored agent node (e.g., second Ubuntu VM)
* Email alert configuration
* Graph widget setup and alert simulations
* GitHub Actions or CI pipeline integration

---

## 🏷️ Tags

`Zabbix` `Monitoring` `Ubuntu` `VirtualBox` `MySQL` `Apache` `Portfolio Lab` `Sysadmin`

---

## ⚖️ License

This project is licensed under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).

You are free to:

* **Share** — copy and redistribute the material in any medium or format
* **Adapt** — remix, transform, and build upon the material for any purpose, even commercially

Under the following terms:

* **Attribution** — You must give appropriate credit, provide a link to the license, and indicate if changes were made.

---


