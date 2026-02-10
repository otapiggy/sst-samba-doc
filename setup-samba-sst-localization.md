# Setup Samba Server and Clients for SST Localization

## 1. Overview

This document describes how to set up a **Samba-based file sharing environment**
for **SST localization**, covering both **server-side** and **client-side**
configurations.

The objective of this setup is to provide a **simple, reliable, and cross-platform
file sharing solution** that enables:

- A Linux system to serve as a centralized Samba file server
- Windows and Linux clients to access shared SST localization data
- Consistent and synchronized access to shared files across different operating systems

### Architecture Overview

![Samba architecture for SST localization](images/samba-architecture.png)

---

## 2. Install and Configure Samba Server

This section describes how to install and configure a **Samba server** on a Linux
system to provide a shared directory for SST localization data.

### 2.1 Prerequisites

- Supported Linux distribution (e.g. Ubuntu 20.04+)
- sudo or root privileges
- Network connectivity to client machines

### 2.2 Install Samba Packages

```bash
sudo apt update
sudo apt install -y samba
```

Verify installation:

```bash
smbd --version
```

### 2.3 Create the Samba Export Directory

```bash
sudo mkdir -p /srv/samba/share
sudo chown -R root:sambashare /srv/samba
sudo chmod -R 2775 /srv/samba
```

### 2.4 Configure Samba Share

Edit configuration:

```bash
sudo nano /etc/samba/smb.conf
```

Append:

```ini
[sst-share]
   path = /srv/samba/share
   browseable = yes
   read only = no
   guest ok = no
   valid users = @sambashare
```

### 2.5 Create Samba Users

```bash
sudo useradd -M -s /usr/sbin/nologin sstuser
sudo usermod -aG sambashare sstuser
sudo smbpasswd -a sstuser
sudo smbpasswd -e sstuser
```

### 2.6 Restart and Enable Services

```bash
sudo systemctl restart smbd nmbd
sudo systemctl enable smbd nmbd
```

### 2.7 Verify Samba Share

```bash
smbclient -L localhost -U sstuser
```
