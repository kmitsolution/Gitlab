**step-by-step hands-on guide** to set up and use the **GitLab Kubernetes Agent** (GKA) to deploy to your Kubernetes cluster using GitLab.

---

## 🎯 Goal

- Connect your Kubernetes cluster to GitLab using the **GitLab Kubernetes Agent**
- Deploy a simple app using GitOps or CI/CD

---

## 🛠️ Prerequisites

- A Kubernetes cluster (EKS, GKE, Minikube, etc.)
- `kubectl` configured and connected to your cluster
- A GitLab project
- GitLab Premium (for full agent features), though some are available in Free tier

---

## 🔁 STEP 1: Register the Agent in GitLab

1. Go to your **GitLab Project** → **Infrastructure → Kubernetes clusters**
2. Click **Connect a cluster (agent-based)**
3. Click **Register a new agent**
4. Give it a name, e.g., `my-agent`
5. GitLab will show you:
   - `agent_id`
   - `registration_token`
   - a `config.yaml` path (e.g., `.gitlab/agents/my-agent/config.yaml`)

---

## 🐳 STEP 2: Install Agent in Kubernetes

1. Clone your GitLab project locally if needed:

```bash
git clone https://gitlab.com/yourname/yourproject.git
cd yourproject
```

2. Create the `gitlab-agent` namespace:

```bash
kubectl create namespace gitlab-agent
```

3. Create the secret using your **registration token**:

```bash
kubectl -n gitlab-agent create secret generic gitlab-agent-token \
  --from-literal=token=PASTE_YOUR_REGISTRATION_TOKEN_HERE
```

4. Apply the official manifest:

```bash
kubectl apply -f https://gitlab.com/gitlab-org/cluster-integration/gitlab-agent/-/raw/main/manifests/agent.yaml
```

✅ Your agent is now installed in the cluster and securely connected to GitLab.

---

## 📁 STEP 3: Add GitOps Config to Your Repo

Create the config file:

```yaml
# .gitlab/agents/my-agent/config.yaml
gitops:
  manifest_projects:
    - id: your-namespace/your-project
      default_namespace: default
```

> Replace `your-namespace/your-project` with your actual GitLab group/project path.

Commit and push it:

```bash
mkdir -p .gitlab/agents/my-agent
nano .gitlab/agents/my-agent/config.yaml  # paste the config above
git add .
git commit -m "Add GitLab Agent config"
git push
```

---

## 📦 STEP 4: Add Kubernetes Manifests to Be Synced

Create a directory with your app’s deployment:

```yaml
# k8s/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx
          ports:
            - containerPort: 80
```

Push this into your GitLab project under `k8s/`.

---

## 📡 STEP 5: GitLab Agent Syncs the Manifests

Once pushed:

- The **GitLab Kubernetes Agent** will detect the change
- It will **pull and apply** the Kubernetes manifests from Git
- You’ll see the app running in your cluster (use `kubectl get pods` to verify)

---

## 🔍 Validate Connection in GitLab

Back in GitLab:

- Go to **Infrastructure → Kubernetes clusters**
- You should see your cluster is **connected** and synced
- You can browse workloads and logs

---

## ✅ Summary

| Step | What Happened |
|------|---------------|
| Register Agent | GitLab generates a token and config path |
| Install Agent | Deployed agent into your cluster |
| Add Config | GitLab knows where to pull manifests |
| GitOps Works | GitLab auto-syncs Kubernetes manifests to your cluster |

---
