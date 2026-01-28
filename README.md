# 🚀 Xeniosrahi Infra Bundle – Raspberry Pi 5 (ARM64)

This repository provides a **turn-key infrastructure stack** for Raspberry Pi 5 (ARM64).  
It sets up developer tooling, infra services, RDP desktop access, monitoring, and deployment scaffolding — all via a **one-click, non-interactive installer**.

---

## 📦 Features

- **System & Networking**
  - Hostname auto-set from `.env`
  - Cloudflare DDNS (systemd service)
  - Automatic TLS with Let’s Encrypt (DNS-01)
  - Subdomains provisioned automatically:
    - `status.$APEX_HOST` → Infra dashboard  
    - `db.$APEX_HOST` → MySQL Community (with Adminer UI)  
    - `rdp.$APEX_HOST` → GNOME RDP session  
    - `bnb.$APEX_HOST` → Example containerized app  
    - `code.$APEX_HOST` → VS Code Web IDE  
    - `ftp.$APEX_HOST` → FTP/FTPS/SFTP service  

- **Core Services**
  - Nginx reverse proxy with TLS termination  
  - MySQL Community (strictly, no MariaDB)  
  - Adminer Web UI (Basic Auth protected)  
  - GNOME on Xorg with xrdp (remote desktop)  
  - Status dashboard with system metrics + service links  
  - FTP/FTPS (vsftpd) + SFTP via OpenSSH  
  - `bnb` placeholder app container  

- **Developer Stack**
  - Flutter web starter  
  - VS Code Server (`code.$APEX_HOST`)  
  - Local kiosk mode (Pi boots to Chromium on status page)  

- **Security**
  - Basic Auth for Adminer + VS Code  
  - TLS via DNS-01 with auto-renew  
  - All configs ARM64-compatible  

---

## ⚙️ Installation

### 1. Clone or download
```bash
git clone https://github.com/yourusername/xeniosrahi-infra.git
cd xeniosrahi-infra
```

### 2. Configure environment
Copy the example `.env` file:
```bash
cp install.example.env install.env
```

Edit `install.env`:
```ini
# Required
APEX_HOST=example.com
ZONE_NAME=example.com
CF_API_TOKEN=your-cloudflare-api-token
CF_ZONE_ID=your-cloudflare-zone-id

# TLS cert lineage
CERT_NAME=xeniosrahi-san

# Service credentials
CODE_USER=dev
CODE_PASSWORD=strongpassword
ADMINER_USER=admin
ADMINER_PASSWORD=strongpassword

# FTP credentials
FTP_USER=ftpuser
FTP_PASSWORD=ftppassword
```

> **Note:** All inputs are taken from `.env`. After installation, no prompts occur.

### 3. Run installer
```bash
sudo ./install_all_noninteractive.sh ./install.env
```

This will:
- Configure hostname + hosts  
- Install DDNS updater  
- Issue TLS certs  
- Deploy Docker services (MySQL, FTP, `bnb`)  
- Enable GNOME + RDP  
- Launch Nginx reverse proxy  

---

## 🌐 Access Services

| Service         | URL                              | Notes                  |
|-----------------|----------------------------------|------------------------|
| Infra Dashboard | https://status.$APEX_HOST        | System health + links  |
| Apex Site       | https://$APEX_HOST               | Flutter web starter    |
| MySQL DB        | https://db.$APEX_HOST            | Adminer UI via status  |
| VS Code Server  | https://code.$APEX_HOST          | Browser IDE (BasicAuth)|
| RDP Desktop     | rdp.$APEX_HOST (RDP client)      | GNOME Xorg             |
| FTP/FTPS        | ftp.$APEX_HOST                   | TLS-secured FTP        |
| SFTP            | sftp ftpuser@ftp.$APEX_HOST      | via OpenSSH            |
| bnb App         | https://bnb.$APEX_HOST           | Example app container  |

---

## 🛠 Operations

- **Check DDNS logs**
  ```bash
  journalctl -u cf-ddns.service
  ```

- **Force TLS renewal**
  ```bash
  sudo certbot renew --dns-cloudflare
  ```

- **Restart services**
  ```bash
  docker compose -f services/docker-compose.yml restart
  ```

- **MySQL data** lives in `/opt/mysql_data` (host-mounted).

---

## 📋 Requirements

- Raspberry Pi 5 (ARM64)  
- Ubuntu Server 22.04+  
- Cloudflare account (API token + Zone ID)  
- Domain pointing to your Cloudflare zone  

---

## 📜 License

MIT License. See [LICENSE](LICENSE).

---

## 🤝 Contributing

Pull requests welcome!  
For major changes, open an issue first to discuss.
