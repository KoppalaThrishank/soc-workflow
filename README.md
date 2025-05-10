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
Clone and Start Mythic
bash
Copy
Edit
git clone https://github.com/its-a-feature/Mythic
cd Mythic
sudo ./install_docker_ubuntu.sh
sudo docker-compose up -d

Access: http://localhost:7443

Default credentials: mythic_admin / password

🔵 2. ELK Stack (Log Monitoring)
Install Java
bash
Copy
Edit
sudo apt install openjdk-17-jdk -y
Add Elastic Repo and Install ELK
bash
Copy
Edit
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list

sudo apt update
sudo apt install elasticsearch logstash kibana -y
sudo systemctl enable --now elasticsearch kibana logstash
Logstash Config to Ingest Logs
bash
Copy
Edit
sudo nano /etc/logstash/conf.d/logstash.conf
conf
Copy
Edit
input {
  file {
    path => "/var/log/syslog"
    type => "syslog"
  }
}
output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index => "logs-%{+YYYY.MM.dd}"
  }
}
bash
Copy
Edit
sudo systemctl restart logstash
Access Kibana: http://localhost:5601

🟢 3. osTicket (Incident Tracking)
Install LAMP Stack
bash
Copy
Edit
sudo apt install apache2 mariadb-server php php-mysql php-curl php-gd php-mbstring php-xml php-zip unzip -y
sudo mysql_secure_installation
Download & Configure osTicket
bash
Copy
Edit
wget https://github.com/osTicket/osTicket/releases/download/v1.18/osTicket-v1.18.zip
unzip osTicket-v1.18.zip
sudo mv upload /var/www/html/osticket
sudo chown -R www-data:www-data /var/www/html/osticket
Create Database
bash
Copy
Edit
sudo mysql -u root -p
sql
Copy
Edit
CREATE DATABASE osticket_db;
CREATE USER 'osticket_user'@'localhost' IDENTIFIED BY 'YourPassword123!';
GRANT ALL PRIVILEGES ON osticket_db.* TO 'osticket_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
Finish Setup in Browser
Open: http://localhost/osticket/setup

Follow the setup wizard and then:

bash
Copy
Edit
sudo rm -rf /var/www/html/osticket/setup
🔄 Optional Integration: ELK ➝ osTicket Alerts
Python example to auto-create tickets:

python
Copy
Edit
import requests

url = "http://localhost/osticket/api/tickets.json"
headers = {
    "X-API-Key": "your_api_key_here"
}
data = {
    "name": "SOC Alert",
    "email": "alert@example.com",
    "subject": "Suspicious Activity Detected",
    "message": "Check Kibana for suspicious log patterns."
}

response = requests.post(url, json=data, headers=headers)
print(response.status_code, response.text)
📄 Sample Resume Bullet Points
✅ Built a cybersecurity lab integrating Mythic C2, ELK Stack, and osTicket to simulate red/blue team activities and SOC workflows.

✅ Automated log ingestion, threat detection, and ticket generation for incident response.

✅ Used Docker, Logstash, and Python to build an end-to-end alerting and ticketing pipeline.

📚 What's Next?
Add Wazuh HIDS or TheHive for advanced threat detection and case management

Trigger real-time alerts from ELK to osTicket via custom scripts or APIs

Simulate phishing, privilege escalation, and lateral movement

🤝 Credits
Mythic C2

ELK Stack

osTicket


