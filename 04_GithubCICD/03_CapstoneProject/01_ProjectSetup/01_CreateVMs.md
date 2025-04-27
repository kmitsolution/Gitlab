# 🧾 Capstone Project Infrastructure Plan (with Software Setup)

## ☁️ Cloud Provider
**Platform:** AWS (Amazon Web Services)  
**Key Pair:** Use an existing AWS Key Pair or create a new one  
**Security Group:**  
- **Inbound:** All traffic (protocol: ALL, port range: ALL, source: `0.0.0.0/0`)  
- **Outbound:** All traffic (default)

---

## 🖥️ Virtual Machines Configuration

### 1️⃣ GitLab Runner Server
- **Purpose:**  
  Runs GitLab self-hosted runner to execute CI/CD pipelines.
- **AMI:** Ubuntu Server 24.04 LTS (64-bit)
- **Instance Type:** `t2.medium` or `t3.medium`
- **Disk Size:** 15 GB
- **Key Pair:** Your chosen key
- **Tags:**  
  - Name: `GitLab-Runner-VM`  
  - Role: `CI`
- **Initial Software Installation:**
  ```bash
  sudo apt update
  sudo apt install openjdk-21-jdk -y
  sudo apt install maven -y
  ```

---

### 2️⃣ Development Server
- **Purpose:**  
  Used for building, compiling code, and Docker image creation.
- **AMI:** Ubuntu Server 24.04 LTS (64-bit)
- **Instance Type:** `t2.medium` or `t3.medium`
- **Disk Size:** 15 GB
- **Key Pair:** Your chosen key
- **Tags:**  
  - Name: `Dev-Server-VM`  
  - Role: `Development`
- **Initial Software Installation:**
  ```bash
  sudo apt update
  sudo apt install openjdk-21-jdk -y
  sudo apt install maven -y
  ```

---

### 3️⃣ Test Server
- **Purpose:**  
  Used for static code analysis (SonarQube, SAST) and other testing tools.
- **AMI:** Ubuntu Server 24.04 LTS (64-bit)
- **Instance Type:** `t2.medium` or `t3.medium`
- **Disk Size:** 15 GB
- **Key Pair:** Your chosen key
- **Tags:**  
  - Name: `Test-Server-VM`  
  - Role: `Testing`
- **Initial Software Installation:** *(to be installed as needed)*

---

## 🛠️ Software Installation Plan

- Java (OpenJDK 21) and Maven installed upfront on Dev and CI servers.
- Other tools like Docker, GitLab Runner, SonarQube, Ansible, Terraform, etc. will be installed step-by-step during project tasks.

---

Would you like this in Markdown format for your README, or as a downloadable PDF?
