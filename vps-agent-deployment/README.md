# Wazuh VPS Deployment with Remote Windows Agent

## Overview

This project documents the deployment of a **Wazuh SIEM server on a public VPS** and the successful enrollment of a **remote Windows Wazuh agent** over the internet.

The goal of this setup was to:
- Run Wazuh **24/7** on a cloud VPS
- Enroll a Windows endpoint located on a different network
- Validate real-world agent-to-manager communication over public IP
- Troubleshoot common agent enrollment and connectivity issues

This mirrors a **real SOC / enterprise deployment model**, where endpoints report to a centralized SIEM hosted in the cloud.

## Architecture

This deployment consists of two main components:

### 1. Wazuh Manager (Server)
- Hosted on a **Contabo Cloud VPS**
- Public IPv4 address exposed to the internet
- Runs the full Wazuh stack:
  - Wazuh Manager
  - Wazuh Indexer
  - Wazuh Dashboard
- Accessible via browser using the VPS public IP

The VPS runs continuously (24/7) and acts as the centralized SIEM server.

### 2. Windows Endpoint (Agent)
- Windows 11 machine on a separate external network
- Connected over the public internet (no local LAN access to the VPS)
- Wazuh agent installed and configured to communicate with the VPS public IP
- Agent sends security telemetry (logs, syscheck, SCA, system inventory)

### Network Model
- Agent → Manager communication over TCP port **1514**
- Enrollment performed via Wazuh agent authentication
- No private networking or VPN required
- Mimics a real-world enterprise remote endpoint setup

## Problems Encountered and Lessons Learned with Docker and VPS installation of Wazuh

During the deployment process, several issues were encountered. These problems were valuable learning experiences and helped reinforce understanding of Wazuh agent enrollment, networking, and identity management.

### 1. Duplicate Agent Names
- Multiple enrollment attempts were made using the same agent name
- Wazuh requires **unique agent names**
- This caused repeated errors such as:
  - `Duplicate agent name`
  - Agent stuck in "never connected" state

**Resolution:**
- Removed stale agents from the manager using `manage_agents`
- Re-enrolled the agent with a unique name
- Verified successful key generation before starting the service

---

### 2. Confusion Between Agent Registration and Connectivity
- Agent keys were successfully generated, but the agent did not appear as active
- This created confusion between:
  - Enrollment (key exchange)
  - Runtime connectivity (agent_control)

**Resolution:**
- Learned that `agent_control -lc` only shows **connected agents**
- Used `manage_agents -l` to confirm enrollment
- Verified connectivity via agent logs and dashboard

---

### 3. Local Docker vs Public VPS Networking
- Initial attempts were made using Docker on a local machine
- Network complexity (NAT, campus firewall, VPN routing) caused instability
- Agents could reach the manager intermittently but not reliably

**Resolution:**
- Migrated Wazuh Manager to a public Contabo VPS
- Used a public IPv4 address for enrollment and communication
- Eliminated NAT and local firewall complications

---

### 4. Agent Restart and Key Sync Issues
- Restarting the agent without cleaning old keys caused authentication failures
- Stale `client.keys` files prevented proper re-enrollment

**Resolution:**
- Stopped the Wazuh agent service
- Removed old `client.keys`
- Re-enrolled cleanly using `agent-auth`
- Restarted the service after successful key validation


## Deployment and Agent Enrollment

### Step 1: VPS Preparation
- A Contabo Cloud VPS was provisioned with a public IPv4 address
- Required ports were allowed:
  - TCP 1514 (agent communication)
  - TCP 1515 (agent enrollment)
  - TCP 443 (dashboard access)

### Step 2: Wazuh Server Installation
- Wazuh was installed on the VPS using the official installation script
- The installation deployed:
  - Wazuh Manager
  - Wazuh Indexer
  - Wazuh Dashboard
- Dashboard access was verified via HTTPS using the VPS public IP

### Step 3: Windows Agent Installation
- Wazuh agent was downloaded and installed on the Windows endpoint
- The agent was configured to point to the VPS public IP as the manager

### Step 4: Agent Enrollment
- The agent was enrolled using the Wazuh authentication process
- A unique agent name was used to avoid conflicts
- A valid authentication key was successfully received from the manager

### Step 5: Agent Activation
- The Wazuh agent service was started on the Windows endpoint
- The agent established a persistent connection to the manager
- The agent appeared as **Active** in the Wazuh dashboard

## Verification and Results

After deployment, the following checks were performed to verify successful operation:

### Agent Connectivity
- The Windows agent successfully connected to the Wazuh Manager over TCP
- No connection or authentication errors were observed in the agent logs

### Manager-Side Verification
- The enrolled agent appeared in the Wazuh Manager as active
- Agent status was confirmed via:
  - Wazuh Dashboard
  - Manager-side agent listing tools

### Dashboard Confirmation
- The agent appeared as **Active (green)** in the Wazuh Dashboard
- System information, security events, and configuration assessments were visible
- This confirmed end-to-end communication between agent and manager
