Here's a **complete step-by-step guide** to connecting a Kubernetes cluster to GitLab using the **GitLab Kubernetes Agent** (`agentk`) with the setup you described, including:

- Creating and registering the agent (`agent-new`)
- Installing GitLab’s agent using **Helm**
- Configuring the agent for your project
- Writing a working `.gitlab-ci.yml` that deploys manifests to EKS via the agent

---

## 📘 Step-by-Step: GitLab Kubernetes Agent Setup (`agent-new`)

---

### 🧱 1. Prerequisites

- You have a working **Kubernetes cluster** (e.g., AWS EKS)
- Helm is installed locally
- You are a **Maintainer** of the GitLab project
- You have GitLab project: `myfirstgroup7711117/capstone`

---

### 🚀 2. Create a GitLab Kubernetes Agent from GitLab UI

1. Go to **GitLab → Infrastructure → Kubernetes Clusters**
2. Click **Connect a cluster (agent)**
3. Set **Agent name** to `agent-new`
4. GitLab will show the commands to install the agent on your cluster using Helm
5. Note the **registration token** GitLab gives you (used in next step)

---

### 📦 3. Install GitLab Agent in EKS (via Helm)

#### 🔧 Add GitLab Helm repo:

```bash
helm repo add gitlab https://charts.gitlab.io
helm repo update
```

#### ⚙️ Install the agent (replace placeholders):

```bash
helm upgrade --install gitlab-agent gitlab/gitlab-agent \
  --namespace gitlab-agent \
  --create-namespace \
  --set config.token=<REGISTRATION_TOKEN_FROM_UI> \
  --set config.kasAddress=wss://kas.gitlab.com
```

> ✅ This installs the agent in your EKS cluster and registers it with GitLab.

---

### 📁 4. Create Agent Configuration in Your Repo

Create a config file at: `.gitlab/agents/agent-new/config.yaml`

#### 📄 `.gitlab/agents/agent-new/config.yaml`

```yaml
ci_access:
  projects:
    - id: myfirstgroup7711117/capstone
```

> This allows CI jobs in your project to deploy using this agent.

> 🔒 Commit and push this file to your default branch (e.g., `main` or `master`)

---

### ✅ 5. Confirm Agent is Connected

After installing the Helm chart and pushing the config, go to:

**GitLab → Infrastructure → Kubernetes Clusters → agent-new**

You should see ✅ “Agent is connected.”

---

### 🛠 6. Configure `.gitlab-ci.yml` for Deployment

#### 📄 `.gitlab-ci.yml`

```yaml
variables:
  KUBE_CONTEXT: myfirstgroup7711117/capstone:agent-new

stages:
  - configure
  - deploy

aws-config-job:
  stage: configure
  tags:
    - gitlab-proj
  script:
    - aws configure set aws_access_key_id "${AWS_ACCESS_KEY_ID}"
    - aws configure set aws_secret_access_key "${AWS_SECRET_ACCESS_KEY}"
    - aws configure set default_region "${AWS_DEFAULT_REGION}"

deploy:
  stage: deploy
  tags:
    - gitlab-proj
  needs:
    - aws-config-job
  script:
    - echo "Using KUBE_CONTEXT: $KUBE_CONTEXT"
    - kubectl config use-context "$KUBE_CONTEXT"
    - kubectl apply -f deploy.yaml
```

> 🔐 Set these variables in **GitLab → Settings → CI/CD → Variables**:
> - `AWS_ACCESS_KEY_ID`
> - `AWS_SECRET_ACCESS_KEY`
> - `AWS_DEFAULT_REGION`

---

### 📈 7. Deploy Flow Summary

1. `aws-config-job` sets up AWS credentials (useful if anything in the pipeline uses AWS)
2. `deploy` job connects to the cluster using the GitLab Agent context
3. `kubectl apply` applies your `deploy.yaml` to the cluster

---

### ✅ Verification

After the pipeline runs:
- Run `kubectl get svc` to get the LoadBalancer external IP
- Visit the app via the IP shown under `EXTERNAL-IP`

---

Would you like to add Helm-based deployments (`helm install`) instead of `kubectl apply`?
