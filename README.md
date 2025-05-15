 🔐 **Cybersecurity Lab Project: Integrating Mythic C2, ELK Stack & osTicket**

This project demonstrates a complete SOC simulation environment integrating:

- 🛠️ **Mythic C2** – for Red Team attack simulation  
- 📊 **ELK Stack** – for Blue Team log monitoring and threat detection  
- 🎫 **osTicket** – for incident management and SOC ticketing  

 📌 Project Goals

- Simulate real-world cyberattacks using Mythic C2 (e.g., reverse shells, beaconing).  
- Detect malicious activities using ELK Stack (Elasticsearch, Logstash, Kibana).  
- Manage and track incidents using osTicket (SOC workflow simulation).  

 🖥️ Lab Environment

- **OS**: Ubuntu 22.04 LTS  
- **Deployment**: Virtual Machine / Cloud (AWS, Azure, DigitalOcean)  

 🔴 1. Mythic C2 (Red Team – Attack Simulation)

 ⚙️ Setup Mythic C2

sudo apt update && sudo apt upgrade -y
sudo apt install docker.io docker-compose git -y
sudo systemctl enable --now docker
git clone https://github.com/its-a-feature/Mythic
cd Mythic
sudo ./install_docker_ubuntu.sh
sudo docker-compose up -d


* Access Mythic: `http://localhost:7443`
* Default credentials: `mythic_admin / password` (or check `.env` file)

### 📦 Install Agent (Apollo)

```bash
sudo docker exec -it mythic_server bash  
./mythic-cli install github https://github.com/MythicAgents/apollo  
exit  
sudo docker restart mythic_server


* Access the Mythic panel from a Windows browser using Ubuntu’s IP.
* Download and execute the payload to simulate a reverse shell.



## 🔵 2. ELK Stack (Log Monitoring)

### 📥 Install Java & ELK Stack


sudo apt install openjdk-17-jdk -y

wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg

echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list

sudo apt update
sudo apt install elasticsearch logstash kibana -y
sudo systemctl enable --now elasticsearch kibana logstash
```

 ⚙️ Logstash Config


sudo nano /etc/logstash/conf.d/logstash.conf

paste this on config file 
```conf
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
```

Restart Logstash:

sudo systemctl restart logstash

* Access Kibana: `http://localhost:5601`

---

## 🟢 3. osTicket (Incident Tracking)

### 🛠️ Install LAMP Stack


sudo apt install apache2 mariadb-server php php-mysql php-curl php-gd php-mbstring php-xml php-zip unzip -y
sudo mysql_secure_installation


### 📦 Download & Configure osTicket


wget https://github.com/osTicket/osTicket/releases/download/v1.18/osTicket-v1.18.zip
unzip osTicket-v1.18.zip
sudo mv upload /var/www/html/osticket
sudo chown -R www-data:www-data /var/www/html/osticket


### 🗃️ Create Database


sudo mysql -u root -p

 Add this Inside the  MySQL:

CREATE DATABASE osticket_db;
CREATE USER 'osticket_user'@'localhost' IDENTIFIED BY 'YourPassword123!';
GRANT ALL PRIVILEGES ON osticket_db.* TO 'osticket_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;

* Open browser: `http://localhost/osticket/setup`
* Complete setup, then:


sudo rm -rf /var/www/html/osticket/setup


## 🔄 ELK ➝ osTicket Alerts (Python Script)

 script python
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


# 🔐 Cybersecurity Workflow Overview

## 1. 🚨 Mythic C2 (Red Team)

* Setup Docker & Mythic
* Create Payload (e.g., Apollo)
* Deploy payload on Windows → Reverse Shell

## 2. 🛡️ ELK Stack (Blue Team)

* Install & configure ELK Stack
* Install **Winlogbeat** on Windows
* Forward Windows logs → Logstash → Elasticsearch
* View logs and alerts in Kibana

### Example Winlogbeat Config:

```yaml
output.logstash:
  hosts: ["<ubuntu_ip>:5044"]
```

### Example Logstash Input Config:

```conf
input {
  beats {
    port => 5044
  }
}
output {
  elasticsearch {
    hosts => ["localhost:9200"]
  }
}
```

* Access Kibana: `http://localhost:5601`
* Set index pattern: `winlogbeat-*`

## 3. 🎫 osTicket (Incident Response)

* Install and configure LAMP stack
* Setup osTicket with database
* Kibana triggers Python script to create alert-based ticket in osTicket

![image alt](https://github.com/KoppalaThrishank/soc-workflow/blob/afe24227f015f84885f45e5d3c19a3fd93f13dc2/Picture1.png)

## 🔄 Workflow

1. 🟥 **Red Team (Mythic)** initiates attack
2. 🟦 **Blue Team (ELK)** detects behavior
3. ⚠️ **Alert** is triggered in Kibana
4. 🎫 **osTicket** receives alert via script
5. 👩‍💻 SOC team investigates & responds

---

## 🔮 Future Enhancements

* Integrate **Wazuh HIDS** for deeper endpoint visibility
* Real-time alerting using **webhooks/APIs**
* Simulate advanced threats: phishing, lateral movement, privilege escalation

---

## 🤝 Credits

* [Mythic C2](https://github.com/its-a-feature/Mythic)
* [ELK Stack](https://www.elastic.co/what-is/elk-stack)
* [osTicket](https://github.com/osTicket/osTicket)

```

---

Let me know if you'd like a `LICENSE`, `requirements.txt`, or `diagram.png` section added as well.
```
