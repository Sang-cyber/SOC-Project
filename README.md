﻿# Soc-Project
Sure! Below is a detailed step-by-step guide on how to set up the **SOC (Security Operations Center) Analyst project**, along with the necessary scripts and tools for each part of the project. This guide will help you monitor network traffic, simulate cyber-attacks, detect incidents, and respond in a simulated environment.

### **Project Setup Tools:**
You’ll need the following tools:

- **SIEM (Security Information and Event Management):**
  - **Splunk** or **ELK Stack** (Elasticsearch, Logstash, Kibana)
- **Intrusion Detection System (IDS) / Intrusion Prevention System (IPS):**
  - **Snort** or **Suricata**
- **Endpoint Detection and Response (EDR):**
  - **OSSEC** or **Wazuh**
- **Packet Analyzer:**
  - **Wireshark**
- **Attacker Machine:**
  - **Kali Linux** (used to simulate attacks like port scanning, brute force attacks, and phishing)

---

### **1. Set Up SIEM (Splunk/ELK Stack)**

#### **1.1 Install Splunk**
Splunk is a popular SIEM tool for log analysis. Follow the steps below to install and configure it.

1. **Download Splunk:**  
   [Download Splunk](https://www.splunk.com/en_us/download.html) for your OS.

2. **Install Splunk:**  
   For Linux:
   ```bash
   dpkg -i splunk-package.deb
   ```

3. **Start Splunk:**
   ```bash
   sudo /opt/splunk/bin/splunk start
   ```

4. **Access Splunk Web Interface:**  
   Go to `http://localhost:8000` and log in with the default credentials.

5. **Add Data Sources:**  
   - Configure **log sources** like firewalls, network devices, and operating systems to forward logs to Splunk.
   - Use **Universal Forwarder** to send logs from your machines to Splunk.

#### **1.2 Install ELK Stack (Elasticsearch, Logstash, Kibana)**
Alternatively, use the ELK Stack (Elasticsearch for storage, Logstash for log collection, Kibana for analysis).

1. **Install Elasticsearch:**
   ```bash
   sudo apt-get install elasticsearch
   sudo systemctl start elasticsearch
   ```

2. **Install Logstash:**
   ```bash
   sudo apt-get install logstash
   ```

3. **Install Kibana:**
   ```bash
   sudo apt-get install kibana
   sudo systemctl start kibana
   ```

4. **Configure Logstash for Collecting Logs:**
   Create a file `/etc/logstash/conf.d/logstash.conf`:
   ```bash
   input {
     beats {
       port => 5044
     }
   }
   
   output {
     elasticsearch {
       hosts => ["localhost:9200"]
       index => "logs-%{+YYYY.MM.dd}"
     }
   }
   ```

5. **Start Kibana Web Interface:**  
   Go to `http://localhost:5601` and start visualizing logs.

---

### **2. Set Up IDS/IPS (Snort or Suricata)**

#### **2.1 Install Snort:**

1. **Install Snort on Ubuntu:**
   ```bash
   sudo apt-get update
   sudo apt-get install snort
   ```

2. **Configure Snort:**
   Edit `/etc/snort/snort.conf`:
   ```bash
   var HOME_NET 192.168.1.0/24
   var EXTERNAL_NET any
   ```
   Update the rule path:
   ```bash
   include /etc/snort/rules/local.rules
   ```

3. **Create a Simple Snort Rule:**
   Edit the file `/etc/snort/rules/local.rules`:
   ```bash
   alert tcp any any -> $HOME_NET 22 (msg:"SSH attempt detected"; sid:1000001; rev:1;)
   ```

4. **Run Snort:**
   ```bash
   snort -A console -q -c /etc/snort/snort.conf -i eth0
   ```

#### **2.2 Install Suricata (Alternative to Snort)**

1. **Install Suricata:**
   ```bash
   sudo apt-get install suricata
   ```

2. **Configure Suricata:**
   Edit `/etc/suricata/suricata.yaml`:
   ```yaml
   HOME_NET: "[192.168.1.0/24]"
   ```

3. **Start Suricata:**
   ```bash
   sudo suricata -c /etc/suricata/suricata.yaml -i eth0
   ```

4. **Check Logs:**  
   Suricata logs can be found in `/var/log/suricata/`.

---

### **3. Install EDR (OSSEC or Wazuh)**

#### **3.1 Install OSSEC:**

1. **Install OSSEC:**
   ```bash
   curl -O https://bintray.com/ossec/ossec-hids/download_file?file_path=ossec-hids-3.6.0.tar.gz
   tar -xvzf ossec-hids-3.6.0.tar.gz
   cd ossec-hids-3.6.0
   ./install.sh
   ```

2. **Configure OSSEC to Monitor Endpoints:**
   Edit `/var/ossec/etc/ossec.conf`:
   ```xml
   <localfile>
     <log_format>syslog</log_format>
     <location>/var/log/auth.log</location>
   </localfile>
   ```

3. **Start OSSEC:**
   ```bash
   /var/ossec/bin/ossec-control start
   ```

4. **Check Logs:**
   Logs can be found in `/var/ossec/logs/alerts/alerts.log`.

#### **3.2 Install Wazuh (Alternative to OSSEC):**

1. **Install Wazuh:**  
   Follow the [official documentation](https://documentation.wazuh.com/current/installation-guide/index.html) to set up Wazuh.

---

### **4. Use Kali Linux for Simulating Cyber-Attacks**

Install **Kali Linux** in a virtual machine or dual-boot setup to simulate attacks on your network.

#### **4.1 Simulate Port Scanning:**
Use **Nmap** to scan open ports on the target machine:
```bash
nmap -sS -p 22,80,443 192.168.1.100
```

#### **4.2 Simulate Brute Force Attack:**
Use **Hydra** to brute-force SSH login credentials:
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.100
```

#### **4.3 Simulate Phishing Attack:**
Use **SET (Social Engineering Toolkit)** to create a phishing website:
```bash
setoolkit
```
- Choose `Social Engineering Attacks > Website Attack Vectors > Credential Harvester Attack Method`.

---

### **5. Analyze Network Traffic with Wireshark**

1. **Install Wireshark:**
   ```bash
   sudo apt-get install wireshark
   ```

2. **Capture Traffic:**  
   Start capturing traffic on your network interface:
   ```bash
   wireshark
   ```
   Filter traffic by IP or protocol (e.g., `ip.addr == 192.168.1.100`).

---

### **6. Incident Response**

Once attacks are detected via logs, follow the steps below for incident response:

1. **Isolate Affected Systems:**  
   Block the IP address using firewall rules:
   ```bash
   sudo ufw deny from 192.168.1.100
   ```

2. **Analyze Logs:**  
   Use Splunk or ELK to dig into the incident logs, identify the timeline of events, and analyze attack patterns.

3. **Mitigation:**  
   Apply patches, update passwords, and remove compromised accounts.

4. **Report Incident:**  
   Document the entire process, from detection to mitigation, and prepare an incident response report.

---

### **7. Threat Intelligence and Automation (Bonus)**

For advanced automation and threat intelligence:

- **Integrate AlienVault OTX** into your SIEM for threat intelligence feeds.
- Use **Cortex XSOAR** or **Splunk Phantom** to automate response actions like blocking IPs or generating alerts.

---

This script and set of tools will allow you to set up a SOC environment, simulate attacks, detect incidents, and practice responding to them in real-time. You’ll get hands-on experience with industry-standard tools used in SOC environments.
