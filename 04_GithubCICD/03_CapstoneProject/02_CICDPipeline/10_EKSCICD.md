To set up a **GitLab CI/CD pipeline that deploys to an AWS EKS (Elastic Kubernetes Service) cluster** using a **self-hosted GitLab Runner**, follow these high-level steps:

---

## ✅ Goal
Build, push, and deploy your application to **AWS EKS** using **GitLab CI/CD** and a **self-hosted runner** with `kubectl`, `awscli`, and optionally `helm`.

---

## 🧱 1. **Pre-requisites**

- AWS EKS cluster is already set up (`kubectl` works from local).
- Self-hosted GitLab Runner is registered and running (can be a VM, EC2, etc.).
- The runner has:
  - `docker`
  - `kubectl`
  - `awscli` (v2)
  - `eksctl` (optional)
  - (optionally) `helm`
- A Dockerfile and Kubernetes manifests are present in your repo.

---

## 🔐 2. **GitLab CI/CD Variables**

Go to `GitLab → Project → Settings → CI/CD → Variables` and add:

| Variable | Description |
|----------|-------------|
| `AWS_ACCESS_KEY_ID` | IAM user key with EKS & ECR permissions |
| `AWS_SECRET_ACCESS_KEY` | IAM user secret |
| `AWS_DEFAULT_REGION` | E.g., `us-east-1` |
| `ECR_REPO_URI` | `xxxxxxxxxxx.dkr.ecr.us-east-1.amazonaws.com/my-app` |
| `KUBE_CONFIG` (optional) | If you prefer not to generate it on-the-fly |

---

## 🐳 3. **Dockerfile (example)**

```Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "server.js"]
```

---

## 📝 4. **`.gitlab-ci.yml` Example**

```yaml
stages:
  - build
  - deploy

variables:
  IMAGE_TAG: $ECR_REPO_URI:$CI_COMMIT_SHORT_SHA

build:
  stage: build
  script:
    - echo "Logging in to ECR..."
    - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $ECR_REPO_URI
    - echo "Building Docker image..."
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
  tags:
    - self-hosted

deploy:
  stage: deploy
  script:
    - echo "Setting up Kube config..."
    - aws eks update-kubeconfig --name my-eks-cluster --region $AWS_DEFAULT_REGION
    - echo "Deploying to EKS..."
    - kubectl set image deployment/my-deployment my-container=$IMAGE_TAG
    - kubectl rollout status deployment/my-deployment
  tags:
    - self-hosted
  when: manual  # Optional: manual trigger
```

---

## 📁 5. **Kubernetes Manifest Example (`deployment.yaml`)**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 2
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
          image: <placeholder-image>
          ports:
            - containerPort: 3000
```

> The CI job replaces `<placeholder-image>` using `kubectl set image`.

---

## 🧪 Optional Enhancements

- Use **Helm** for templated deployments.
- Add `kubectl apply -f k8s/` to deploy full manifest sets.
- Add test stage before deploy.
- Push Docker image with tag `latest` as well.

---

