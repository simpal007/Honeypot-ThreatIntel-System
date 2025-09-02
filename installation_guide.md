# 📝 Installation Guide – HoneySentinel Honeypot with Threat Intelligence  

This guide explains how to install and configure the **HoneySentinel** project on a Raspberry Pi (or any Linux machine).  
It covers installing dependencies, setting up the honeypot, configuring logging, dashboards, alerts, and threat intelligence enrichment.  

---

## 🔹 1. System Requirements  

- Raspberry Pi 4 (4GB or 8GB recommended) or a Linux VM  
- 16GB+ SD card or disk space  
- Internet connection  
- Docker & Docker Compose installed  

---

## 🔹 2. Install Docker & Docker Compose  

Run the following commands:  

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Docker
curl -fsSL https://get.docker.com | sh

# Enable Docker service
sudo systemctl enable docker
sudo systemctl start docker

# Install Docker Compose
sudo apt install docker-compose -y

#Verify installation
```bash
docker --version
docker-compose --version



## 🔹 3. Install Docker & Docker Compose  

Run the following commands:  

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Docker
curl -fsSL https://get.docker.com | sh

# Enable Docker service
sudo systemctl enable docker
sudo systemctl start docker

# Install Docker Compose
sudo apt install docker-compose -y




