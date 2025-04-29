
## 🚀 Step-by-Step: Maven Package Publishing via GitLab CI/CD

### 🧱 1. **Pre-requisites**
- Your GitLab project should have:
  - A **`pom.xml`** file
  - A valid **Group or Project access token** with `write_package_registry` scope or use `CI_JOB_TOKEN`
- GitLab Package Registry must be enabled

---

### 🗂️ 2. **Add a `.maven/settings.xml` File**
This configures the Maven server for publishing:

```xml
<settings>
  <servers>
    <server>
      <id>gitlab-maven</id>
      <username>${env.CI_JOB_TOKEN}</username>
      <password>${env.CI_JOB_TOKEN}</password>
    </server>
  </servers>
</settings>
```

- This file can be stored in your repo (not recommended for secrets) or passed in the CI job as an artifact or variable.

---

### 🧪 3. **Update Your `pom.xml`**

Add a distributionManagement section:

```xml
<distributionManagement>
  <repository>
    <id>gitlab-maven</id>
    <url>https://gitlab.com/api/v4/projects/PROJECT_ID/packages/maven</url>
  </repository>
</distributionManagement>
```

> Replace `PROJECT_ID` with your actual GitLab **numeric** project ID (from your project settings or URL).

---

### 📝 4. **Sample `.gitlab-ci.yml`**

```yaml
stages:
  - build
  - publish

variables:
  MAVEN_CLI_OPTS: "-s .maven/settings.xml --batch-mode"
  PACKAGE_REGISTRY_URL: "https://gitlab.com/api/v4/projects/$CI_PROJECT_ID/packages/maven"

build:
  image: maven:3.8.6-jdk-11
  stage: build
  script:
    - mvn $MAVEN_CLI_OPTS clean package

publish:
  image: maven:3.8.6-jdk-11
  stage: publish
  script:
    - mvn $MAVEN_CLI_OPTS deploy
  only:
    - main
```

---

### 🔐 5. **Authentication Options**

You can use:
- `CI_JOB_TOKEN` (as shown)
- Or create a **Deploy Token** or **Personal Access Token** with `write_package_registry` and add them as GitLab **CI/CD variables**:
  - `MAVEN_USERNAME`
  - `MAVEN_PASSWORD`

Update `.maven/settings.xml` accordingly if using named tokens.

---

Let me know if you want a `settings.xml` template using deploy tokens or help with using Maven snapshots.
