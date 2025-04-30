 **step-by-step deployment**

- Uses **Ansible** on a **GitLab self-hosted runner**
- Applies Kubernetes manifests (`deploy.yaml`, `service.yaml`) to an **AWS EKS cluster**
- Uses `kubectl` (pre-installed) and assumes `kubeconfig` is already available
- Deploys your Docker image to Kubernetes and exposes it via a **LoadBalancer service**

---

## 📘 Step-by-Step GitLab CI/CD to Deploy to AWS EKS using Ansible

---

### ✅ 1. Prerequisites

Make sure these are already set up:

| Requirement                             | Details                                                                 |
|-----------------------------------------|-------------------------------------------------------------------------|
| **GitLab Self-Hosted Runner**           | Runner tagged `gitlab-proj` and registered to your project              |
| **Runner Host Setup**                   | Has `Ansible`, `kubectl`, and access to EKS via `~/.kube/config`        |
| **AWS EKS Cluster**                     | Exists and has appropriate IAM access (e.g., via instance role or keys) |
| **GitLab Project Files**                | Includes `deploy.yaml`, `service.yaml`, and `app.yaml` Ansible playbook |

---

### ✅ 2. Kubernetes Manifests

#### 📄 `deploy.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deploy
  name: nginx-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-deploy
  template:
    metadata:
      labels:
        app: nginx-deploy
    spec:
      containers:
      - name: webapp
        image: ramansharma95/webapp
        ports:
        - containerPort: 80
```

#### 📄 `service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx-deploy
  name: nginx-deploy
spec:
  selector:
    app: nginx-deploy
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 80
```

---

### ✅ 3. Ansible Playbook to Deploy

#### 📄 `app.yaml`

```yaml
---
- name: Apply Kubernetes Manifests to EKS
  hosts: localhost
  connection: local
  tasks:
    - name: Apply deployment
      command: kubectl apply -f deploy.yaml

    - name: Apply service
      command: kubectl apply -f service.yaml
```

> This runs `kubectl apply` directly on the runner (which is assumed to already have access to your EKS cluster).

---

### ✅ 4. GitLab CI Pipeline

#### 📄 `.gitlab-ci.yml`

```yaml
stages:
  - provision

ansible_run:
  stage: provision
  tags:
    - gitlab-proj  # Ensure the job runs on the self-hosted runner
  script:
    - echo "Running Ansible to deploy to EKS..."
    - ansible --version
    - ansible-playbook app.yaml
```

---

### ✅ 5. GitLab Runner Host Configuration

On the host machine where the self-hosted GitLab runner is installed:

- Ensure `ansible` and `kubectl` are installed and in `PATH`
- Ensure `kubectl` is configured to access EKS:
  ```bash
  kubectl config view
  kubectl get nodes
  ```
- Ensure your IAM role/instance profile has EKS access (`eks:DescribeCluster`, etc.)

---

### ✅ 6. Expected Outcome

When you push to your GitLab repository:

1. GitLab triggers the pipeline.
2. The `ansible_run` job runs on the tagged runner.
3. The `app.yaml` playbook applies `deploy.yaml` and `service.yaml` using `kubectl`.
4. EKS provisions a **LoadBalancer** (usually an AWS Classic or NLB).
5. You can get the external URL using:

   ```bash
   kubectl get svc nginx-deploy
   ```

   It will show an `EXTERNAL-IP` — your application will be available there.

---

### ✅ 7. Optional Enhancements

- Add a `wait_for` check in Ansible to wait until LoadBalancer is assigned.
- Use `kubectl rollout status` to wait for deployment readiness.
- Add notifications (e.g., Slack) after deployment.
- Clean up old versions with labels/selectors.

---

Would you like a version of this that dynamically extracts the LoadBalancer URL and prints it at the end of the pipeline?
