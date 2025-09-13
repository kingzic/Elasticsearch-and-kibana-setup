# Elasticsearch-and-kibana-setup

````markdown
# Elasticsearch and Kibana Setup on Vultr

## Introduction
Elasticsearch and Kibana are part of the Elastic Stack, widely used for log management, monitoring, and security analytics. Setting them up on a cloud platform like Vultr allows organizations to centralize data collection, indexing, and visualization, making it easier to detect issues, analyze performance, and strengthen security monitoring.

---

## Tools Used
- **Vultr Cloud Platform** – for hosting the virtual private cloud (VPC) and virtual machines.  
- **Ubuntu Server** – the operating system used for deployment.  
- **Elasticsearch** – the core engine for indexing and searching log data.  
- **Kibana** – the visualization and dashboard tool for analyzing Elasticsearch data.  
- **PowerShell/SSH** – for remote access and configuration.  
- **Firewall Rules & UFW** – for securing access to Elasticsearch and Kibana services.  

---

## Elasticsearch Setup
1. **Create a VPC** on Vultr and ensure all virtual machines use the same location as the VPC.  
2. **Deploy a new server** with:  
   - Dedicated CPU  
   - Ubuntu image  
   - Disabled backups and IPv6  
   - Attached to the VPC  
3. **Login via SSH** from PowerShell:  
   ```bash
   ssh root@<generated-IP>
````

4. **Update repositories**:

   ```bash
   apt-get update && apt-get upgrade -y
   ```
5. **Download and install Elasticsearch**:

   ```bash
   wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.1.3-amd64.deb
   dpkg -i elasticsearch-9.1.3-amd64.deb
   ```

   Save the generated password securely.
6. **Configure `elasticsearch.yml`** to use the VM IP address.
7. **Set up firewall rules** in Vultr (SSH port 22, TCP rules for access).
8. **Enable and start Elasticsearch service**:

   ```bash
   systemctl daemon-reload
   systemctl enable elasticsearch.service
   systemctl start elasticsearch.service
   systemctl status elasticsearch.service
   ```

---

## Kibana Setup

1. **Download and install Kibana**:

   ```bash
   wget https://artifacts.elastic.co/downloads/kibana/kibana-9.1.3-amd64.deb
   dpkg -i kibana-9.1.3-amd64.deb
   ```
2. **Generate enrollment token** for Kibana:

   ```bash
   cd /usr/share/elasticsearch/bin
   ./elasticsearch-create-enrollment-token --scope kibana
   ```
3. **Access Kibana** via:

   ```
   http://<generated-IP>:5601
   ```

   If blocked, allow port 5601:

   ```bash
   ufw allow 5601
   ```

   And create a firewall rule for TCP (1-65535) in Vultr.
4. **Verify Kibana with code** from:

   ```bash
   /usr/share/kibana/bin/kibana-verification-code
   ```
5. **Login credentials**:

   * Username: `elastic`
   * Password: (the one generated during Elasticsearch installation)
6. **Configure encryption keys**:

   ```bash
   ./kibana-encryption-keys generate
   ./kibana-keystore add <field-name>
   ```

   Repeat for the three xpack keys.
7. **Restart Kibana**:

   ```bash
   systemctl restart kibana.service
   ```

---

## Challenges Faced

* Configuring VPC and firewall rules correctly to avoid access issues.
* Remembering to save and manage generated passwords and tokens.
* Editing configuration files (`elasticsearch.yml` and Kibana settings) properly to match the server IP.
* Handling installation errors due to missing dependencies or incorrect package versions.
* Network access errors such as blocked ports (e.g., port 5601 for Kibana).

---

## What Elasticsearch and Kibana Are Used For

### Elasticsearch

* Stores, indexes, and searches large volumes of data quickly.
* Provides the backend for log analytics, monitoring, and security detection.
* Supports advanced queries for threat hunting and data correlation.

### Kibana

* Acts as the visualization layer for Elasticsearch data.
* Provides dashboards, charts, and search capabilities.
* Enables monitoring, alerting, and security event investigation.

---

## Conclusion

Deploying Elasticsearch and Kibana on Vultr enhances visibility into system and security events. Elasticsearch handles data ingestion and indexing, while Kibana transforms raw data into meaningful dashboards and reports. Despite setup challenges like configuration and firewall rules, the result is a powerful analytics platform that supports monitoring, troubleshooting, and cybersecurity operations.

```

Do you want me to also **export this Markdown into a `.md` file** so you can download and use it directly?
```
