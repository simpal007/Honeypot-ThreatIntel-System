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

# Verify installation
docker --version
docker-compose --version

```
---

## 🔹 3. Clone the Repository  

Clone this project from GitHub and enter the directory:  

```bash
git clone https://github.com/simpal007/Honeypot-ThreatIntel-System.git
cd Honeypot-ThreatIntel-System

```

## 🔹 4. Cowrie Honeypot Setup  

Cowrie is an SSH and Telnet honeypot designed to log brute force attacks and capture attacker activity.  
In **HoneySentinel**, Cowrie acts as the primary honeypot whose logs are forwarded to **Loki** via **Promtail**.  

---

### ➡️ Add Cowrie to Docker Compose  

Update your `docker-compose.yml` to include Cowrie:  

```yaml
  cowrie:
    image: cowrie/cowrie:latest
    container_name: cowrie
    restart: always
    ports:
      - "2222:2222"   # Honeypot SSH port
      - "2223:2223"   # Honeypot Telnet port
    volumes:
      - ./cowrie/etc:/cowrie/cowrie-git/etc
      - ./cowrie/var:/cowrie/cowrie-git/var
```

### ➡️ Configure Cowrie

1. Create a directory structure:

```bash
mkdir -p cowrie/etc cowrie/var
```

2. Copy the default Cowrie configuration:

```bash
docker run --rm cowrie/cowrie:latest cat /cowrie/cowrie-git/etc/cowrie.cfg > cowrie/etc/cowrie.cfg
```

3. Edit cowrie/etc/cowrie.cfg to enable SSH & Telnet honeypot:

```ini
[ssh]
enabled = true
listen_port = 2222

[telnet]
enabled = true
listen_port = 2223
```

### ➡️ Start Cowrie

Run:

```bash
docker-compose up -d cowrie
```

### ➡️ Test the Honeypot

From another machine (or your host), try connecting:

```bash
ssh root@<honeypot-ip> -p 2222
```

Cowrie will log all interactions. Logs are stored in:

```lua
cowrie/var/log/cowrie/cowrie.log
```
---

## 🔹 5. Loki Setup (Log Aggregator)

- Ensure you have docker-compose.yml and loki-config.yaml in the project folder.
- Start Loki with Docker Compose:

```bash
docker-compose up -d loki
```
- Verify Loki is running:

```bash
docker ps | grep loki
```
- Access Loki API:

```arduino
http://<your-server-ip>:3100/metrics
```
---

---

## 🔹 6. Grafana Setup (Dashboard & Visualization)  

Grafana allows you to visualize logs collected by **Promtail** and stored in **Loki**.  

### ➡️ Add Grafana to Docker Compose  

Your `docker-compose.yml` should include the Grafana service:  

```yaml
  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    volumes:
      - grafana-data:/var/lib/grafana
volumes:
  grafana-data:
```
- Start Grafana
  run
  
```bash
docker-compose up -d grafana
```

-Check if Grafana is running:

```bash
docker ps | grep grafana
```

- Access Grafana
  
  Open a browser and go to:

```bash
http://<your-server-ip>:3000
```

- Default credentials:

Username: admin

Password: admin (you will be prompted to change it on first login)

---

## 🔹 7. Promtail Setup (Log Collector)

Promtail is an agent that collects logs from the Honeypot (Cowrie) and forwards them to **Loki** for storage and indexing.  

### ➡️ Add Promtail to Docker Compose  

Your `docker-compose.yml` should include the Promtail service:  

```yaml
  promtail:
    image: grafana/promtail:latest
    volumes:
      - ./promtail-config.yml:/etc/promtail/config.yml
      - /var/log:/var/log
    command: -config.file=/etc/promtail/config.yml
```

### ➡️ Create Promtail Config File

Create a file named promtail-config.yml in the project root directory:

```yaml
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
  - job_name: system-logs
    static_configs:
      - targets:
          - localhost
        labels:
          job: syslog
          __path__: /var/log/*log

  - job_name: cowrie-logs
    static_configs:
      - targets:
          - localhost
        labels:
          job: cowrie
          __path__: /cowrie/var/log/cowrie/cowrie.log
```

### ➡️ Start Promtail

Run:

```bash
docker-compose up -d promtail
```
Verify Promtail is running:

```bash
docker ps | grep promtail
```

### ➡️ Confirm Logs in Loki

Go to Grafana → Explore.

Select Loki as the data source.

Run a query such as:

```logql
{job="cowrie"}
```
---

## 🔹 8. Grafana – Loki Integration  

Grafana is used to visualize and explore the logs stored in Loki.  
By connecting Loki as a **data source** in Grafana, you can create dashboards, run queries, and monitor attacker activities.  

---

### ➡️ Access Grafana  

1. Open Grafana in your browser:

   http://<server-ip>:3000

- Default username: **admin**  
- Default password: **admin** (you’ll be asked to change it on first login).  

---

### ➡️ Add Loki as a Data Source  

1. In Grafana, go to:  
**Configuration → Data Sources → Add Data Source**  
2. Select **Loki**.  
3. Set the URL:  


http://loki:3100

4. Click **Save & Test**.  
Grafana should confirm the connection.  

---

### ➡️ Explore Logs  

1. Go to **Explore** in Grafana.  
2. Select **Loki** as the data source.  
3. Run a query to check if logs are flowing:  
```yaml
{job="cowrie"}

or

{job="syslog"}

```

### ➡️ Create Dashboards

Navigate to Dashboards → New Dashboard → Add a Panel.

Choose Loki as the data source.

Example queries:

  Failed SSH login attempts:

```yaml
{job="cowrie"} |= "failed login"
```
  Attacker IP addresses:

```yaml
{job="cowrie"} | json | line_format "{{.src_ip}}"
```























