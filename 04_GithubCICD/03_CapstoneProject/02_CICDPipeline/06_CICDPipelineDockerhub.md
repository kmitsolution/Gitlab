To **dockerize a project using GitLab CI/CD**, you'll need to:

1. **Write a Dockerfile** to define how your app is containerized.
2. **Use GitLab's CI/CD pipeline** to build the Docker image and optionally push it to a **Docker Registry** (e.g., GitLab Container Registry).
3. Define the process in your **`.gitlab-ci.yml`** file.

---

## ✅ Example: Dockerization Using GitLab CI/CD

### 📁 1. **Dockerfile**

Place this in your project root:

```Dockerfile
# Example: Dockerfile for a Java Spring Boot app
FROM openjdk:17-jdk-slim
COPY target/myapp.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

> Adjust for your tech stack (Node.js, Python, Go, etc.)

---

### ⚙️ 2. **.gitlab-ci.yml**

```yaml
stages:
  - build
  - dockerize

variables:
  DOCKER_DRIVER: overlay2
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA

# Build your app (Java example)
build:
  stage: build
  image: maven:3.8.6-jdk-17
  script:
    - mvn clean package -DskipTests
  artifacts:
    paths:
      - target/*.jar

# Docker build and push
dockerize:
  stage: dockerize
  image: docker:latest
  services:
    - docker:dind
  before_script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
  script:
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
```

---

### 🔐 3. **CI/CD Variables Setup in GitLab**

Go to **Settings > CI/CD > Variables**, and add:

| Key                 | Value                                         |
|---------------------|-----------------------------------------------|
| `CI_REGISTRY_USER`  | your GitLab username or deploy token name     |
| `CI_REGISTRY_PASSWORD` | personal access token or deploy token     |

> GitLab provides `$CI_REGISTRY`, `$CI_REGISTRY_IMAGE`, etc., automatically.

---

### 📦 Result:
- On each push, GitLab will:
  - Build your app
  - Create a Docker image
  - Push it to your project’s **Container Registry** at  
    `registry.gitlab.com/<namespace>/<project>`

---

