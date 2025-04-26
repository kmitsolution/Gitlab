## 🏗️ **GitLab Architecture Overview**

GitLab has **two main components** in a CI/CD context:

---

### 🖥️ 1. **GitLab Server**

This is the **central control system** — the brain.

#### 🔧 Responsibilities:
- Manages **Git repositories** (code hosting)
- Provides the **web interface** (UI)
- Handles **Merge Requests**, Issues, Wiki, CI/CD pipeline configs
- **Schedules jobs** to be executed
- Stores artifacts, logs, container registry, and more

💡 Think of it as the "boss" that manages your projects and delegates work to runners.

---

### ⚙️ 2. **GitLab Runner**

This is the **worker** — it runs the CI/CD jobs.

#### 🔧 Responsibilities:
- **Listens** for jobs from GitLab Server
- **Executes** pipeline steps (build, test, deploy)
- Sends results/logs **back to the GitLab Server**

#### 🧰 Types of Runners:
- **Shared Runner** – Available to all projects on GitLab
- **Specific Runner** – Dedicated to one project/group
- Can run using different **executors**:
  - **Shell**
  - **Docker**
  - **Kubernetes**
  - **VirtualBox**
  - etc.

💡 You can install runners **on any machine or VM**, not necessarily on the GitLab server itself.

---

## 🔁 How They Work Together

```
[Developer pushes code] 
        ↓
[GitLab Server detects change]
        ↓
[.gitlab-ci.yml triggers pipeline]
        ↓
[GitLab Server schedules jobs]
        ↓
[GitLab Runner picks job → executes]
        ↓
[Runner sends results back to server]
        ↓
[Results shown in GitLab UI]
```

---

### 🧩 Summary Table

| Component       | Purpose                                         |
|----------------|--------------------------------------------------|
| **GitLab Server** | Hosts code, UI, pipelines, schedules jobs      |
| **GitLab Runner** | Executes jobs from pipeline on external machine |

---

Would you like a downloadable PDF summary of this or a visual sketch to use in a presentation?
