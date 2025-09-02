# 🐝 HoneySentinel – Honeypot with Threat Intelligence  

**HoneySentinel** is a security research project that combines a **Raspberry Pi-based honeypot** with **centralized logging, visualization, alerting, and threat intelligence enrichment**.  
It is designed to **capture real-world attacks**, analyze malicious activity, and provide insights into attacker behavior through an SOC-style monitoring pipeline.  

---

## 🔹 Features  
- **Honeypot (Cowrie)** – Captures SSH & Telnet brute force attempts, executed commands, and uploaded files.  
- **Centralized Logging (Promtail + Loki)** – Collects and stores logs for analysis.  
- **Visualization (Grafana)** – Dashboards to monitor attacks in real-time.  
- **Threat Intelligence Integration** – Enriches logs with IP reputation and IOCs from external feeds (e.g., AbuseIPDB, AlienVault OTX).  
- **Real-time Alerts (Telegram)** – Sends instant alerts to administrators.  
- **Lightweight Deployment** – Fully containerized using Docker & Docker Compose.  

---

## 🔹 System Architecture  

```text
[ Attacker ]  
     ↓  
[ Cowrie Honeypot ]  
     ↓  
[ Promtail → Loki ]  
     ↓  
[ Grafana Dashboards ] ——> [ Administrator ]  
     ↓  
[ Threat Intelligence Enrichment ]  
     ↓  
[ Telegram Alerts ]  
