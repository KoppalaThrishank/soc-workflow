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

# 🔐 Cybersecurity Workflow: Mythic C2 + ELK Stack + osTicket

## 1. 🚨 Mythic C2 (Red Team – Attack Simulation)

### 1.1. ⚙️ Setup Mythic C2
- Install **Docker**, **Mythic**, and necessary dependencies.
- Start **Mythic C2** using Docker.
- Access Mythic at `http://localhost:7443`.  You can find the password on .env file inside Mythic
 
 ![image alt]( https://github.com/KoppalaThrishank/soc-workflow/blob/f7ffc6c489fb6093c0fd02920f284283c7f3c75a/Picture1.png)
  
 

### 1.2. 🎯 Create Payload
- Using **Mythic**, create a custom payload (e.g., **Apollo** for Linux or a Windows agent).
- Configure the payload to connect back to **Mythic C2** for command-and-control operations.
  ![image alt](https://github.com/KoppalaThrishank/soc-workflow/blob/f7ffc6c489fb6093c0fd02920f284283c7f3c75a/Picture2.png)
 

### 1.3. 💻 Access Target System
- Deploy the payload to a **Windows machine via Mythic C2.
- The payload executes on the target system, establishing a reverse shell connection to Mythic.
(That payload does establish a reverse shell connection back to the Mythic C2 server (this is how Mythic works—like any C2)


## 2. 🛡️ ELK Stack (Blue Team – Monitoring and Detection)

### 2.1. 🖥️ Install ELK Stack
- Install **Elasticsearch**, **Logstash**, and **Kibana** on a monitoring server.
 
After installation open the  sudo nano /etc/elasticsearch/elasticsearch.yml
You need to set network host : 0.0.0.0 
- Set up **Winlogbeat** on the target **Windows machine** to forward **Windows Event Logs** (security, application, and system logs) to the ELK Stack.

### 2.2. 📝 Configure Winlogbeat
- Install and configure **Winlogbeat** on the target **Windows system**.
•	- Configure Winlogbeat, Then it forwards those logs to Logstash, which sends them to Elasticsearch.
•	You can view and analyze those logs in Kibana.
Open in notepad Winlogbeat 
yaml
output.logstash:
  hosts: ["localhost:5044"] or hosts: [ubuntu ip:5044]


### 2.3. 🔄 Log Processing in Logstash

* **Logstash** processes incoming logs and forwards them to **Elasticsearch**.
* In **Logstash**, set up input and output configurations to handle Windows Event logs.
Open logstash log file 
input {
  beats { port => 5044 }
}
output {
  elasticsearch { hosts => ["localhost:9200"] }
}

### 2.4. 📊 View Logs in Kibana

* Access **Kibana** at `http://localhost:5601`.
* Configure **index patterns** (e.g., `winlogbeat-*`) to view logs from Winlogbeat.
* Create **visualizations** and **alerts** in Kibana to monitor for suspicious activity (e.g., unexpected command executions, unusual logins).
 


## 3. 🎫 osTicket (Incident Response – Ticketing)

### 3.1. ⚙️ Setup osTicket

* Install the **LAMP stack** (Apache, MariaDB, PHP) on the server hosting **osTicket**.
* Download and configure **osTicket** for incident tracking and response.
* Set up the **osTicket database** and configure its settings via the web interface.

### 3.2. 🔍 Monitor and Detect Malicious Activity

* Once malicious activity is detected via **Kibana** (e.g., unusual behavior, command execution), **Kibana** will trigger an alert.
![Uploading image.png…]()

### 3.3. 📝 Create Incident Ticket

* The alert can be forwarded automatically to **osTicket** using a **Python script 
 
* Example Python script to create a ticket in osTicket based on a Kibana alert:

```python
import requests

url = "http://localhost/osticket/api/tickets.json"
data = {
    "name": "SOC Alert",
    "email": "alert@example.com",
    "subject": "Suspicious Activity Detected",
    "message": "Check Kibana for suspicious log patterns."
}

response = requests.post(url, json=data, headers={"X-API-Key": "your_api_key_here"})
print(response.status_code, response.text)
```

### 3.4. ✅ Incident Ticket Created in osTicket

* The ticket is created with details like the name of the alert, the type of activity, and a link to **Kibana logs**.
 
* This ticket will be visible in **osTicket's** dashboard for SOC analysts to review, investigate, and take further action.
 
## 🔄 Flow of Events

1. **Red Team (Mythic C2)** initiates an attack by deploying a payload.
2. **Blue Team (ELK Stack)** detects suspicious activity through logs forwarded by **Winlogbeat** and processed by **Logstash**.
3. An **alert** is generated in **Kibana** based on the detected activity (e.g., payload execution).
4. **osTicket** is automatically triggered to create an incident ticket detailing the suspicious activity.
5. The **SOC team** reviews the ticket in **osTicket**, investigates the issue, and takes action based on the analysis.


## 🔮 Future Enhancements

* Integrate **Wazuh HIDS** for advanced threat detection.
* Set up real-time alerts from **ELK** to **osTicket** using custom scripts or API webhooks.
* Simulate additional attack techniques such as **phishing**, **privilege escalation**, and **lateral movement**.

