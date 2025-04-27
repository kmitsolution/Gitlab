In GitLab CI/CD (`.gitlab-ci.yml`), conditional logic is handled using a few different mechanisms depending on what you're trying to do.

Here are the main ways to write **conditional statements** in a GitLab pipeline:

---

### 🔹 1. **`rules:` (Recommended over `only`/`except`)**
`rules:` let you run jobs conditionally based on branch, variables, file changes, etc.

```yaml
job_name:
  script: echo "This runs conditionally"
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
      when: always
```

You can use logical operators like `==`, `!=`, `&&`, `||`, etc.

---

### 🔹 2. **Using `only` and `except` (older style, still supported)**
```yaml
job_name:
  script: echo "Only runs on main"
  only:
    - main
```

But `rules:` is more flexible and preferred in modern pipelines.

---

### 🔹 3. **Conditionals inside scripts**
If you want to do logic inside a job's script:

```yaml
job_name:
  script:
    - |
      if [ "$CI_COMMIT_BRANCH" = "main" ]; then
        echo "Running on main"
      else
        echo "Not main"
      fi
```

---

### 🔹 4. **Using `needs`, `allow_failure`, or `when:`**
For conditional job flow or dependencies.

---

