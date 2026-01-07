# Wazuh SIEM Deployment with Docker (Single Node)

## Overview
This project demonstrates hands-on Security Operations Center (SOC) experience by deploying **Wazuh SIEM** in a **Docker-based single-node environment** on Windows. The lab covers Wazuh architecture, secure deployment using certificates, and access to the Wazuh dashboard for monitoring and alerting.

The goal of this project is to showcase practical skills in SIEM deployment, log management, and containerized security infrastructure—skills directly relevant to junior SOC analyst and cybersecurity roles.

---

## Technologies Used
- **Wazuh** (SIEM / XDR)
- **Docker & Docker Compose**
- **Windows OS**
- **Git**

---

## Wazuh Architecture (Simple Explanation)
- **Wazuh Agent** – Collects logs and security events from endpoints
- **Wazuh Server** – Analyzes logs using decoders and rules
- **Wazuh Indexer** – Stores and indexes security data
- **Wazuh Dashboard** – Web interface for monitoring alerts and system status

**Analogy:**
- Agent → Security cameras & sensors
- Server → Control center
- Indexer → Video storage
- Dashboard → Monitoring screens

---

## Deployment Model
**Single-node Docker deployment**
- All Wazuh components run on a single host
- Each component runs in its own container
- Suitable for labs, testing, and learning environments

---

## Prerequisites
- Docker Desktop
- Git for Windows
- Minimum **4 GB RAM**
- At least **10 GB free disk space**

---

## Deployment Steps

### 1. Clone the Wazuh Docker Repository
```bash
git clone https://github.com/wazuh/wazuh-docker.git
```

Navigate to the single-node deployment directory:
```bash
cd wazuh-docker/single-node
```

---

### 2. Generate Self-Signed Certificates
Wazuh uses certificates to secure communication between components. For this lab, self-signed certificates are used.

```bash
docker compose -f generate-indexer-certs.yml run --rm generator
```

---

### 3. Start the Wazuh Stack
```bash
docker compose up -d
```

This launches the Wazuh server, indexer, and dashboard containers.

---

### 4. Access the Wazuh Dashboard
Open a browser and navigate to:
```
https://localhost
```

A browser security warning is expected due to self-signed certificates.
- Click **Advanced**
- Select **Proceed to localhost (unsafe)**

Log in using the default credentials provided in the Wazuh documentation.

---

## Validation
- Wazuh dashboard accessible via browser
- All containers running successfully
- Indexer receiving and storing data
- Dashboard displaying system status

---

## SOC Relevance
This project demonstrates:
- SIEM deployment and configuration
- Log ingestion and analysis workflow
- Docker-based security tooling
- Understanding of Wazuh architecture and components

---

## Future Improvements
- Deploy and enroll Wazuh agents
- Generate and analyze security alerts
- Add custom detection rules
- Forward logs from Windows endpoints

---

## Disclaimer
This deployment is intended for **learning and lab purposes only** and should not be used in production without proper hardening and security controls.

---

## Author
**[Your Name]**

---

