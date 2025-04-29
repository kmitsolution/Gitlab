To **push a Docker image to the GitLab Container Registry** using **GitLab CI/CD**, follow this complete setup. It builds the image and pushes it automatically to:

```
registry.gitlab.com/<your-namespace>/<your-project>
```

---

## ✅ Step-by-Step Setup

---

### 📁 1. **Dockerfile**

Place in your repo root:

```Dockerfile
# Example: Python app
FROM python:3.11-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

---

### 📝 2. **.gitlab-ci.yml**

```yaml
stages:
  - dockerize

variables:
  DOCKER_DRIVER: overlay2
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA

dockerize:
  image: docker:24.0.5
  stage: dockerize
  services:
    - docker:24.0.5-dind
  before_script:
    - echo "$CI_REGISTRY_PASSWORD" | docker login -u "$CI_REGISTRY_USER" --password-stdin $CI_REGISTRY
  script:
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
```

---

### 🔐 3. **CI/CD Variables Setup**

Go to **GitLab → Project → Settings → CI/CD → Variables**, and add:

| Variable Name         | Value                                  |
|------------------------|----------------------------------------|
| `CI_REGISTRY_USER`     | Your GitLab username or deploy token  |
| `CI_REGISTRY_PASSWORD` | Your GitLab [personal access token](https://gitlab.com/-/profile/personal_access_tokens) or deploy token |

> Your token needs at least `write_registry` scope.

---

### 🧪 4. **Pipeline Output**

On each push to your repo:
- GitLab builds your Docker image.
- Pushes it to the **GitLab Container Registry** at:

```
registry.gitlab.com/<group>/<project>:<short-sha>
```

---

### 🐳 View Your Image:

Go to **Project → Packages & Registries → Container Registry**  
You’ll see the pushed image there, ready for pull/use.

---

