# Zabbix Monitoring Lab - Manual Deployment

This project documents the manual setup of a fully functional Zabbix 6.0 LTS monitoring server running on Ubuntu Server 22.04, hosted on a VirtualBox virtual machine. The lab demonstrates how to configure system monitoring from the ground up, with hands-on work at every stage.

---

## Overview

This lab simulates a production-style environment for monitoring servers, services, and systems using Zabbix. It includes manual installation of Ubuntu Server 22.04 (LTS), installation and configuration of the Zabbix server, frontend, and agent, MySQL database setup with security hardening, an Apache web frontend for dashboard access, and bridged networking for external browser access.

---

## Before vs. After

Before: A fresh Ubuntu Server with no monitoring in place, no visibility into server health, services, or potential outages.

After: A fully functional Zabbix 6.0 LTS server with a MySQL backend and Apache-served dashboard, actively monitoring the host and ready to onboard additional agent nodes.

---

## System Requirements

Host OS: Windows 10/11. Virtualization Tool: VirtualBox (latest version). ISO Used: ubuntu-22.04.5-live-server-amd64.iso. VM Specs: 2 GB RAM, 1 CPU, 30 GB dynamically allocated disk, Bridged Network Adapter (Ethernet preferred).

---

## Step-by-Step Setup

### 1. Ubuntu Server Installation

Selected "Ubuntu Server" (not minimized), configured LVM with full disk, enabled OpenSSH Server, and skipped featured snaps (e.g., Docker, Kubernetes).

### 2. Network Configuration

Bridged Adapter enabled in VirtualBox. Physical Ethernet used (Wi-Fi bridge may not work reliably). Verified IP via ip a after reboot.

### 3. MySQL Configuration

Installed MySQL via apt and ran mysql_secure_installation, removing anonymous users, disallowing root remote login, and removing the test database. Logged into MySQL as root and created the Zabbix DB:

CREATE DATABASE zabbix character set utf8mb4 collate utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'YOUR_PASSWORD_HERE';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;

---

## Zabbix Installation

Repository & Packages:

wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo apt update

Install Zabbix Server, Frontend, and Agent:

sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-agent mysql-server -y

Import Initial Database Schema:

zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -u zabbix -p zabbix

Configure Zabbix Server (set DBPassword in /etc/zabbix/zabbix_server.conf):

sudo nano /etc/zabbix/zabbix_server.conf

Start and Enable Services:

sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2

---

## Accessing the Dashboard

Get the IP address with ip a and use the inet value under the bridged interface (e.g., 192.168.x.x or 10.x.x.x). Open the Zabbix UI on the host machine browser at http://your-vm-ip/zabbix. Login with username Admin and the admin password chosen during setup.

---

## Lessons Learned / Troubleshooting

VirtualBox "Port Forwarding" UI sometimes fails to display, a bridged adapter with Ethernet is more reliable. Cloud-init sometimes traps users in "bash: systemctl: command not found", switching to manual install avoids this. Ctrl+C and q are lifesavers for exiting stuck command prompts or paged output. Password mismatches and multiline SQL input can cause MySQL errors, so it pays to be meticulous.

---

## Screenshots Included

VirtualBox Network Configuration, Ubuntu Language Selection, Network Configuration During Install, User and Hostname Setup, Storage Configuration (LVM), OpenSSH Server Enabled During Ubuntu Installation, Final Install Summary, IP Address Confirmation via ip a, Zabbix Login Page, and Zabbix Dashboard Overview.

![VirtualBox Network](screenshots/virtualbox-network.png)
![Ubuntu Language](screenshots/ubuntu-language-selection.png)
![Network Config](screenshots/ubuntu-network-config.png)
![User Setup](screenshots/ubuntu-user-setup.png)
![LVM Setup](screenshots/ubuntu-lvm-setup.png)
![OpenSSH Enabled](screenshots/ubuntu-openssh-selected.png)
![Install Summary](screenshots/ubuntu-install-summary.png)
![IP Address](screenshots/ip-address-confirmation.png)
![Zabbix Login](screenshots/zabbix-login.png)
![Zabbix Dashboard](screenshots/zabbix-dashboard.png)

---

## Future Enhancements

Add a monitored agent node (e.g., a second Ubuntu VM), configure email alerts, set up graph widgets and alert simulations, and integrate GitHub Actions or a CI pipeline.

---

## Tags

Zabbix, Monitoring, Ubuntu, VirtualBox, MySQL, Apache, Portfolio Lab, Sysadmin

---

## License

This project is licensed under the Creative Commons Attribution 4.0 International License (CC BY 4.0). You are free to share (copy and redistribute the material in any medium or format) and adapt (remix, transform, and build upon the material for any purpose, even commercially), under the condition of attribution: you must give appropriate credit, provide a link to the license, and indicate if changes were made.
