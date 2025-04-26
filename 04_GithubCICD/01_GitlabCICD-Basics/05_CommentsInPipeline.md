# Comments in Gitlab pipeline file
---

### ✏️ How to write comments

In `.gitlab-ci.yml`, **comments** are written by **starting the line with `#`**.

Anything after `#` on that line is **ignored** by GitLab.

---

### 🛠️ Example:

```yaml
# This is a simple GitLab CI/CD pipeline example

stages:     # Defining the order of stages
  - build
  - test

build_phase:   # First job to build the project
  image: alpine
  stage: build
  script:
    - echo "Building the project..."
    - mkdir target
    - echo "<h1>Build Completed</h1>" > target/index.html
  artifacts:    # Save the build output for next jobs
    paths:
      - target/

testing_phase: # Second job to test the built files
  image: alpine
  stage: test
  script:
    - echo "Testing the project..."
    - cat target/index.html
```

---

### 🔥 Key Points:
- `#` can be placed **anywhere** — before jobs, inside jobs, or at the top.
- **Only** that line is treated as a comment (not multi-line comments).
- Comments are **ignored** during the pipeline execution.

---

### ❓Bonus Tip:
If you want to **temporarily disable** a script line during testing/debugging, you can also "comment" that line with `#` inside `script`.

Example:
```yaml
script:
  - echo "Running build"
  # - some-failing-command  # Temporarily disabled
```

---

