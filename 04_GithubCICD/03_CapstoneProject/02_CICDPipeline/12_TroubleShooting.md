**troubleshooting** and **handling job timeouts** in **GitLab CI/CD**, with simple examples and best practices.

---

## 🧰 1. **Troubleshooting GitLab CI/CD Pipelines**

When a job fails, here are common things to check:

| 🔧 Issue | 🔍 Troubleshooting Steps |
|---------|--------------------------|
| **Job fails to start** | Check runner status (Settings → CI/CD → Runners) |
| **Permission denied** | Ensure SSH key or AWS credentials are set in CI/CD variables |
| **Command not found** | Check image used and install required tools |
| **Network errors** | Check if the runner has internet/VPC access |
| **Docker build fails** | Check Dockerfile syntax and image versions |

---

### 🧪 Example: Missing `mvn` Command

```yaml
build:
  script:
    - mvn clean install
```

💥 Error:
```
mvn: command not found
```

✅ Fix: Use an image that has Maven preinstalled.

```yaml
image: maven:3.8.6-openjdk-17
```

---

## ⏳ 2. **Handling Job Timeout in GitLab CI/CD**

By default, GitLab jobs have a timeout (e.g., 1 hour). You can override it:

### 📝 Example 1: Set Job Timeout in `.gitlab-ci.yml`

```yaml
build:
  script:
    - ./long-running-script.sh
  timeout: 30 minutes
```

### 📝 Example 2: Global Timeout (Project-wide)

In GitLab UI:

> **Project → Settings → CI/CD → General Pipelines → Timeout**

Set the timeout for *all* jobs (e.g., 1 hour).

---

## ⚠️ Common Timeout Troubleshooting

| Scenario | Fix |
|---------|-----|
| Job times out waiting for input | Add `when: manual` and run manually |
| Long Docker builds | Use `cache` and `artifacts` to speed up |
| Command hangs (e.g., no output) | Use `echo` or `--verbose` options for visibility |
| Git clone times out | Use a shallow clone: `GIT_DEPTH: 1` |

---

### ✅ Example: Keep Output to Avoid Timeout (for silent builds)

```yaml
script:
  - ./build.sh | tee build.log
```

Or force periodic output:

```bash
while true; do echo "still running..."; sleep 60; done &
./your_script.sh
```

Great! Here's a **complete `.gitlab-ci.yml` template** that includes:

- 🧱 Basic pipeline stages  
- 🔁 Caching for faster builds  
- ⏱ Timeout handling  
- 🪵 Output logging to avoid job hangs  
- ✅ Manual deployment (optional)

---

## ✅ Full `.gitlab-ci.yml` Template (Java Maven Example)

```yaml
stages:
  - build
  - test
  - deploy

variables:
  MAVEN_OPTS: "-Dmaven.repo.local=.m2/repository"
  GIT_DEPTH: "1"  # Faster Git clones

# Cache dependencies
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - .m2/repository

build-job:
  stage: build
  image: maven:3.8.6-openjdk-17
  timeout: 20 minutes
  script:
    - echo "Starting build..."
    - mvn clean compile | tee build.log
    - echo "Build completed."
  artifacts:
    paths:
      - target/
      - build.log

test-job:
  stage: test
  image: maven:3.8.6-openjdk-17
  timeout: 15 minutes
  script:
    - echo "Running tests..."
    - mvn test | tee test-results.log
    - echo "Tests done."
  artifacts:
    paths:
      - test-results.log
    when: always

deploy-job:
  stage: deploy
  image: alpine:latest
  timeout: 10 minutes
  before_script:
    - echo "Simulating deploy..."
    - apk add --no-cache openssh
  script:
    - echo "Deploying to server..."
    - while true; do echo "Still deploying..."; sleep 60; done &
    - sleep 120  # simulate deployment
    - echo "Deploy finished."
  when: manual
  only:
    - main
```

---

## 🔍 Key Features Explained

| Feature | Purpose |
|--------|---------|
| `timeout:` | Limits job runtime to prevent long hangs |
| `tee` | Logs output while still printing to the console |
| `while true...` | Keeps job from being marked as idle (good for quiet scripts) |
| `cache:` | Speeds up Maven by saving dependencies |
| `when: manual` | Makes deployment job require manual trigger from GitLab UI |
| `GIT_DEPTH` | Makes Git clone faster by not pulling full history |

---



---
