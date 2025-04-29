---

## 📘 GitLab CI/CD Job Documentation: `maven-package`

### Purpose

This GitLab CI/CD job compiles and create capstone.jar file a Java Maven project and stores the build output (e.g., compiled `.class` files and resources) in a specific directory on the **self-hosted GitLab Runner's Ubuntu server**. The output is made available for use in later pipeline stages or by other jobs.

---

### Job Definition

```yaml
variables:
  BUILD_OUTPUT_DIR: /opt/build-artifacts/project-name

stages:
  - build

maven-compile:
  stage: build
  tags:
    - capstone-project  # Tag used to match the specific GitLab runner
  script:
    - mkdir -p "$BUILD_OUTPUT_DIR"
    - mvn compile
    - cp -r target/* "$BUILD_OUTPUT_DIR/"
```

---

### How It Works

1. **Stage Declaration:**
   - This job belongs to the `build` stage, where code compilation occurs.

2. **Tag Usage:**
   - The job is assigned a runner with the tag `capstone-project`. Your self-hosted GitLab runner must be registered with this tag.

3. **Custom Output Directory:**
   - The environment variable `BUILD_OUTPUT_DIR` defines where on the server to store compiled output.
   - You can modify this path as needed for your environment (e.g., `/var/lib/gitlab-runner/builds/myproject/`).

4. **Maven Compile:**
   - Runs `mvn compile` to compile the source code. By default, Maven outputs compiled files into the `target/` directory.

5. **Artifact Copying:**
   - Copies everything from `target/` to the specified `$BUILD_OUTPUT_DIR` for later reuse.

---

### Requirements

#### ✅ GitLab Runner Configuration

- The GitLab runner **must be configured with the `shell` executor** (or another executor that allows access to host directories).
- The runner must have:
  - Write access to the target output directory (`$BUILD_OUTPUT_DIR`)
  - Sufficient permissions to run `mvn`

#### ✅ Directory Setup

Ensure the output directory exists and is writable by the GitLab runner user:

```bash
sudo mkdir -p /opt/build-artifacts/project-name
sudo chown gitlab-runner:gitlab-runner /opt/build-artifacts/project-name
```

> Replace `gitlab-runner` with your actual runner user if different.

---

### Notes

- This approach persists output **only on the runner host**. If you use a Docker executor or multiple runners, consider using GitLab `artifacts` instead (see alternative setup below).
- You can access this directory from later stages by referencing the same `$BUILD_OUTPUT_DIR`.

---

### 🔁 Alternative: Using GitLab Artifacts

For portability and safer pipelines, consider using artifacts instead of writing to a host directory:

```yaml

stages:
  - build
maven-compile:
  stage: build
  tags:
    - capstone-project
  script:
    - mvn clean package
  artifacts:
    paths:
      - target/   # assuming 'mvn compile' outputs to the 'target' directory
    expire_in: 1 hour  # optional
```

This stores the `target/` directory as a downloadable artifact that is automatically passed to later jobs.

---

Would you like me to generate a downloadable `.md` version of this documentation for you?
