 **dependencies in GitLab CI/CD pipelines**, with a focus on **caching Maven dependencies** to speed up builds.

---

## ✅ 1. **Handling Dependencies in CI/CD Pipelines**

In CI/CD, dependencies (like Maven `.jar` files) are fetched during every build unless:
- They are **cached** locally
- Or you use a **custom package registry**

This can **slow down pipelines** and increase bandwidth usage.

---

## 🚀 2. **Caching and Reusing Dependencies**

GitLab CI supports **`cache`** and **`artifacts`** to store files between jobs.  
Caching Maven's `.m2` directory lets you **reuse downloaded dependencies**, speeding up builds.

---

## 🛠️ 3. **Hands-on: Cache Maven Dependencies in GitLab Pipelines**

### 💡 Project Structure

Assume you have a typical Java project using Maven with a `pom.xml`.

---

### 📝 `.gitlab-ci.yml` Example

```yaml
stages:
  - build

variables:
  MAVEN_OPTS: "-Dmaven.repo.local=.m2/repository"

build:
  image: maven:3.8.6-openjdk-17
  stage: build
  cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - .m2/repository
  script:
    - mvn clean install
  artifacts:
    paths:
      - target/
```

---

### 🔍 Explanation

- `MAVEN_OPTS` sets the local Maven repo to a subdir: `.m2/repository`
- `cache` saves Maven dependencies between builds
- `key` allows separate caches per branch (`CI_COMMIT_REF_SLUG`)
- `artifacts` saves the compiled JAR/WAR for downstream jobs

---

## 🧪 Optional Enhancements

- Use a **shared cache** key (e.g., `key: maven-deps`) if you want all branches to share dependencies
- Add a **`test`** stage to reuse the build artifact

---

