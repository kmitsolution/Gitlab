### 🚀 Introduction to GitLab Kubernetes Agent (GKA)

The **GitLab Kubernetes Agent** is a modern, secure, and **pull-based integration** between GitLab and a Kubernetes cluster. It's designed to:

- Deploy apps to Kubernetes clusters from GitLab
- Monitor cluster state and sync desired config (GitOps-style)
- Support secure CI/CD deployments without exposing your cluster via `kubectl` or external APIs

---

## 🧩 Key Features

| Feature | Description |
|--------|-------------|
| 🔒 **Secure** | Uses **pull-based** connections: the agent connects *outbound* from your cluster to GitLab |
| 🔄 **GitOps** | Automatically sync Kubernetes manifests from a Git repo |
| 🛠️ **CI/CD Integration** | Allows GitLab CI/CD jobs to run Kubernetes commands inside your cluster |
| 📡 **Observability** | GitLab can view cluster status, events, and workloads |

---

## 🛠️ How It Works

1. **Install the Agent** in your Kubernetes cluster using Helm or kubectl
2. **Register the Agent** with your GitLab project or group
3. The agent creates a secure, outbound connection to GitLab via WebSocket
4. CI/CD jobs and GitOps features can now interact with the cluster securely

---

## 🗂️ Use Cases

- Deploying Helm charts or raw manifests from GitLab repos
- Enabling GitOps workflows (pull changes from Git into the cluster)
- Secure `kubectl` access in CI/CD without exposing your cluster publicly

---

## 📋 Requirements

- GitLab Premium or Ultimate for full features (some free tier features are available)
- Kubernetes 1.20+
- Helm 3 (optional but helpful)
- Admin access to your Kubernetes cluster

---

## 🔧 Basic Setup Overview

1. **In GitLab:**
   - Go to your **Project → Infrastructure → Kubernetes clusters**
   - Select **Connect a cluster → Agent-based**
   - Download the agent config and token

2. **In Your Cluster:**

```bash
kubectl apply -f https://gitlab.com/gitlab-org/cluster-integration/gitlab-agent/-/raw/main/manifests/agent.yaml
```

3. **GitOps Config (Optional):**

```yaml
# .gitlab/agents/<agent-name>/config.yaml
gitops:
  manifest_projects:
    - id: mygroup/myproject
      default_namespace: default
```

---

## ✅ Benefits Over Legacy Integration

| Legacy Kubernetes Integration | GitLab Kubernetes Agent |
|------------------------------|--------------------------|
| Push-based (less secure)     | Pull-based (more secure) |
| Manual token/API handling    | Secure handshake via Agent |
| Limited GitOps support       | Native GitOps engine     |

---

