# 🧾 Capstone Project Infrastructure Plan

## ☁️ Cloud Provider
**Platform:** AWS (Amazon Web Services)  
**Region:** As per your preference (e.g., `us-east-1` or `ap-south-1`)  
**Key Pair:** Use an existing AWS Key Pair or create a new one  
**Security Group:**  
- **Name:** `allow-all-traffic`
- **Rules:**  
  - Inbound: All traffic (`0.0.0.0/0`, all ports, all protocols)  
  - Outbound: All traffic (`0.0.0.0/0`, all ports, all protocols)  
  - *Note:* This is acceptable for development/test environments but **not recommended for production**

---

## 🖥️ Virtual Machines Configuration

### 1️⃣ GitLab Runner Server
- **Purpose:**  
  Hosts the GitLab self-hosted runner used to execute CI/CD pipelines for the Capstone project.
- **AMI:** Ubuntu Server 24.04 LTS (64-bit)
- **Instance Type:** `t2.medium` or `t3.medium`
- **vCPU / RAM:** 2 vCPUs, 4 GB RAM
- **Root Disk Size:** 15 GB
- **Key Pair:** Use your specified key
- **Tags:**  
  - Name: `GitLab-Runner-VM`  
  - Role: `CI`

---

### 2️⃣ Development Server
- **Purpose:**  
  Used for development tasks such as building the application, running Maven commands, and Docker builds.
- **AMI:** Ubuntu Server 24.04 LTS (64-bit)
- **Instance Type:** `t2.medium` or `t3.medium`
- **Root Disk Size:** 15 GB
- **Key Pair:** Use your specified key
- **Tags:**  
  - Name: `Dev-Server-VM`  
  - Role: `Development`

---

### 3️⃣ Test Server
- **Purpose:**  
  Serves as a secondary environment for tasks like code analysis, static testing (SonarQube, SAST), or other support services.
- **AMI:** Ubuntu Server 24.04 LTS (64-bit)
- **Instance Type:** `t2.medium` or `t3.medium`
- **Root Disk Size:** 15 GB
- **Key Pair:** Use your specified key
- **Tags:**  
  - Name: `Test-Server-VM`  
  - Role: `Testing`

---

## 🔐 Notes

- Ensure **SSH access** using the correct key pair.
- All machines should have **internet access** (via public IP or NAT) for updates and external dependencies.
- You can later use **Ansible** to provision these VMs with necessary tools (Docker, Java, Maven, GitLab Runner, etc.)

---

