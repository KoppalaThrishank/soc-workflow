# soc-workflow
# 🔐 Cybersecurity Lab: Mythic C2 + ELK Stack + osTicket

This project demonstrates a complete **SOC simulation environment** integrating:

- 🛠️ **Mythic C2** – for Red Team attack simulation  
- 📊 **ELK Stack** – for Blue Team log monitoring and threat detection  
- 🎫 **osTicket** – for incident management and SOC ticketing

---

## 📌 Project Goals

- Simulate real-world cyberattacks using Mythic C2 (e.g., reverse shells, beaconing).
- Detect malicious activities using ELK Stack (Elasticsearch, Logstash, Kibana).
- Manage and track incidents using osTicket (SOC workflow simulation).

---

## 🖥️ Lab Environment

- **OS**: Ubuntu 22.04 LTS  
- **Deployment**: Virtual Machine or Cloud (AWS, Azure, DigitalOcean)

---

## 🚀 Setup Instructions

### 🔴 1. Mythic C2 (Red Team)

#### Install Dependencies
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install docker.io docker-compose git -y
sudo systemctl enable --now docker
