## ✅ Common GitLab CI/CD Job Keywords with Examples

---

### 1. **`stages`**
Defines the order of pipeline execution.

```yaml
stages:
  - build
  - test
  - deploy
```

---

### 2. **`image`**
Specifies the Docker image to use for the job.

```yaml
build:
  stage: build
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn package
```

---

### 3. **`script`**
Main commands to run in the job.

```yaml
test:
  stage: test
  script:
    - echo "Running tests"
    - ./run-tests.sh
```

---

### 4. **`before_script`**
Commands that run **before** the `script`.

```yaml
before_script:
  - echo "Preparing..."

job1:
  script:
    - echo "Doing work"
```

---

### 5. **`after_script`**
Commands that run **after** the `script`, even if the job fails.

```yaml
job1:
  script:
    - ./do-something.sh
  after_script:
    - echo "Cleaning up..."
```

---

### 6. **`artifacts`**
Used to pass files between jobs or to store for download.

```yaml
build:
  stage: build
  script:
    - mvn package
  artifacts:
    paths:
      - target/*.jar
```

---

### 7. **`cache`**
Used to cache dependencies between pipeline runs.

```yaml
cache:
  paths:
    - .m2/repository/
```

---

### 8. **`only` / `except` (deprecated, use `rules` now)**
Control job execution based on branches, tags, etc.

```yaml
job1:
  script: echo "Deploying"
  only:
    - main
```

---

### 9. **`rules`**
More flexible alternative to `only` and `except`.

```yaml
job1:
  script: echo "Hello"
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

---

### 10. **`needs`**
Allows jobs to start early by specifying dependencies.

```yaml
test:
  stage: test
  script: ./test.sh
  needs: [build]
```

---

### 11. **`tags`**
Routes the job to specific GitLab runners.

```yaml
deploy:
  stage: deploy
  tags:
    - prod-runner
  script:
    - ./deploy.sh
```

---

### 12. **`retry`**
Retries a job if it fails.

```yaml
job1:
  script: ./unstable-task.sh
  retry: 2
```

---

### 13. **`timeout`**
Limits how long a job can run.

```yaml
job1:
  script: ./slow-task.sh
  timeout: 10 minutes
```

---

### 14. **`dependencies`**
Allows jobs to download artifacts from previous jobs (alternative to `needs`).

```yaml
deploy:
  stage: deploy
  script: ./deploy.sh
  dependencies:
    - build
```

---



---

Would you like this list as a downloadable cheat sheet or in markdown/PDF format?
