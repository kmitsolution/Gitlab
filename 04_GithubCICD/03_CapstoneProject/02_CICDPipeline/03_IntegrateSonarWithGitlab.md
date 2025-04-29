To integrate **SonarQube** with **GitLab** using Docker (SonarQube image on port 9000), follow these steps:

---

## 🚀 Step-by-Step Integration Guide

### 🧱 1. **Run SonarQube with Docker**
Pull and run the SonarQube Docker container:

```bash
docker run -d --name sonarqube \
  -p 9000:9000 \
  -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true \
  sonarqube:lts
```

- Access SonarQube at: [http://localhost:9000](http://localhost:9000)
- Default credentials:  
  - Username: `admin`  
  - Password: `admin` (you’ll be prompted to change it)

---

### ⚙️ 2. **Create a Project and Generate Token in SonarQube**

1. Log in to SonarQube at `http://localhost:9000`.
2. Go to **Projects** → **Create Project**.
3. Choose **Manually**, enter project name (e.g., `my-gitlab-project`), and key.
4. Follow the instructions until it prompts for a token.
5. Click **Generate token**, give it a name, and copy the **token** (you'll use it in GitLab).

---

### 🛡️ 3. **Create GitLab CI/CD Variables**

In your GitLab project:
1. Go to **Settings** → **CI/CD** → **Variables**.
2. Add the following variables:
   - `SONAR_TOKEN`: your SonarQube token (from step above)
   - `SONAR_HOST_URL`: `http://host.docker.internal:9000`  
     *(or `http://<your-ip>:9000` if running on Linux or outside Docker)*

---

### 🧪 4. **Update `.gitlab-ci.yml`**

Here’s a basic example of a GitLab CI/CD pipeline that includes SonarQube scanning:

```yaml
stages:
  - build
  - sonarqube

variables:
  SONAR_SCANNER_OPTS: "-Dsonar.projectKey=my-gitlab-project"

sonarqube-check:
  image: sonarsource/sonar-scanner-cli
  stage: sonarqube
  script:
    - sonar-scanner \
        -Dsonar.host.url=$SONAR_HOST_URL \
        -Dsonar.login=$SONAR_TOKEN
```

> Replace `my-gitlab-project` with your actual project key used in SonarQube.

---

### ✅ 5. **Push and Verify**

Push your code to GitLab. The pipeline will:
- Pull the scanner image
- Run static analysis
- Send the results to your local SonarQube server

---

### 📝 Optional Notes

- If your code is in Java or other languages, make sure you include additional properties (e.g., `sonar.sources`, `sonar.java.binaries`) in the `.gitlab-ci.yml`.
- You can store those in a `sonar-project.properties` file at the project root instead of putting them all in the CI script.

---

