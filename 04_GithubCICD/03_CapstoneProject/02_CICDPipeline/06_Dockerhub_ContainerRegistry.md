Here's a **complete step-by-step documentation** for setting up your `.gitlab-ci.yml` pipeline for a Spring Boot Maven application. This pipeline:

1. **Builds the project with Maven**
2. **Packages it into a Docker image**
3. **Pushes it to GitLab's Container Registry and Docker Hub**

---

## 🚀 Full CI/CD Pipeline Documentation for Spring Boot App

### 📁 Project Requirements

Before you begin:

- Your project is a Spring Boot Maven project with a `pom.xml`.
- You have a valid `Dockerfile` in the root of the project.
- Your GitLab project is configured to use GitLab CI/CD.

---

### 📦 Project Structure

```
my-spring-app/
├── .gitlab-ci.yml
├── Dockerfile
├── pom.xml
└── src/
```

---

### 🐳 Dockerfile (Place in project root)

```dockerfile
FROM eclipse-temurin:17-jdk

ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar

ENTRYPOINT ["java","-jar","/app.jar"]
```

---

### 🔐 GitLab CI/CD Variables

Set the following in your **GitLab project** under:

```
Settings > CI / CD > Variables
```

| Variable Name      | Value                                    |
|--------------------|------------------------------------------|
| `DOCKERHUB_USERNAME` | Your Docker Hub username                |
| `DOCKERHUB_PASSWORD` | Your Docker Hub [access token](https://hub.docker.com/settings/security) |
| `CI_REGISTRY`        | Default is `registry.gitlab.com` (you can also define it explicitly) |

> ✅ GitLab automatically sets `CI_JOB_TOKEN`, `CI_PROJECT_PATH`, and `CI_COMMIT_SHORT_SHA`.

---

### 🛠 `.gitlab-ci.yml`

```yaml
stages:
  - build
  - dockerize

variables:
  IMAGE_NAME: myapp
  DOCKER_REGISTRY: registry.gitlab.com
  GITLAB_IMAGE: $DOCKER_REGISTRY/$CI_PROJECT_PATH:$CI_COMMIT_SHORT_SHA
  DOCKERHUB_IMAGE: docker.io/ramansharma95/$IMAGE_NAME:$CI_COMMIT_SHORT_SHA

cache:
  paths:
    - .m2/repository/

build:
  stage: build
  image: maven:3.9-eclipse-temurin-17-alpine
  script:
    - mvn clean package -DskipTests
  artifacts:
    paths:
      - target/

docker_build_and_push:
  stage: dockerize
  image: docker:latest
  services:
    - docker:dind
  before_script:
    - docker info
    - echo "$CI_JOB_TOKEN" | docker login -u gitlab-ci-token --password-stdin $CI_REGISTRY
    - echo "$DOCKERHUB_PASSWORD" | docker login -u "$DOCKERHUB_USERNAME" --password-stdin
  script:
    - docker build -t $GITLAB_IMAGE .
    - docker tag $GITLAB_IMAGE $DOCKERHUB_IMAGE
    - docker push $GITLAB_IMAGE
    - docker push $DOCKERHUB_IMAGE
```

---

### ✅ What This Does

| Stage         | Description                                                                 |
|---------------|-----------------------------------------------------------------------------|
| `build`       | Uses Maven to compile your Spring Boot app and create a JAR.               |
| `dockerize`   | Builds a Docker image, then pushes it to GitLab Container Registry and Docker Hub. |

---

### 🔎 Image Tags

Each image will be tagged using the current commit SHA:
- `registry.gitlab.com/groupname/projectname:<commit_sha>`
- `docker.io/ramansharma95/myapp:<commit_sha>`

You can also add `:latest` or Git tags if needed.

---

### ✅ Notes

- This pipeline uses **shared GitLab runners** and `docker:dind` (Docker-in-Docker).
- **Artifacts** from the build stage (JAR file) are passed to the docker stage.
- If you're using a **custom runner**, you can enable Docker layer caching for faster builds.

---

Would you like me to add Git tags or version-based Docker image tagging logic too?
