To **push a Docker image to AWS ECR (Elastic Container Registry)** using **GitLab CI/CD**, you'll need to:

1. Create a **Dockerfile**
2. Set up **ECR repository**
3. Create **AWS credentials** and store them in **GitLab CI/CD variables**
4. Write the **`.gitlab-ci.yml`** to build and push the image

---

## ✅ Step-by-Step: Push Docker Image to AWS ECR from GitLab CI

---

### 🧱 1. **Dockerfile (Example for Node.js)**

```Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "app.js"]
```

---

### 📦 2. **Set Up AWS ECR**

1. Go to **AWS Console > ECR**
2. Create a new repository (e.g., `my-ecr-repo`)
3. Note the repository URL:  
   ```
   <aws_account_id>.dkr.ecr.<region>.amazonaws.com/my-ecr-repo
   ```

---

### 🔐 3. **Set GitLab CI/CD Variables**

Go to **GitLab > Project > Settings > CI/CD > Variables**, and add:

| Variable Name       | Value                      |
|---------------------|----------------------------|
| `AWS_ACCESS_KEY_ID` | Your AWS access key        |
| `AWS_SECRET_ACCESS_KEY` | Your AWS secret key   |
| `AWS_DEFAULT_REGION` | e.g., `us-east-1`         |
| `ECR_REPOSITORY_URI` | Full ECR URL (see above) |

> IAM user must have `AmazonEC2ContainerRegistryFullAccess` permission.

---

### 📝 4. **`.gitlab-ci.yml`**

```yaml
stages:
  - dockerize

variables:
  IMAGE_TAG: $ECR_REPOSITORY_URI:$CI_COMMIT_SHORT_SHA

dockerize:
  image: amazon/aws-cli:2.13.0
  stage: dockerize
  services:
    - docker:24.0.5-dind
  before_script:
    - apk add --no-cache docker
    - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $ECR_REPOSITORY_URI
  script:
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
```

---

### ✅ 5. **What Happens**

- The pipeline installs Docker and AWS CLI
- Logs into AWS ECR
- Builds the Docker image
- Pushes it to your ECR repository

---

