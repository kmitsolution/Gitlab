
# 📄 Running Parallel Jobs in GitLab CI/CD

In GitLab, **jobs inside the same stage run *in parallel***.  
👉 *Each job runs in its **own container**, isolated from others.*

This means:
- If you put `test_artifact` and `unit_tests` in the same stage (`test` stage),
- They **both will run at the same time** in separate containers
- It will **save time** compared to running one after another!

---

# 🔥 Example of Parallel Jobs

Let's modify your `.gitlab-ci.yml` slightly:

```yaml
stages:
  - build
  - test

build_website:
  image: node:22-alpine
  stage: build
  script:
    - node --version
    - npm --version
    - npm ci
    - npm run build
  artifacts:
    paths: 
      - build/
    expire_in: 1 hour

test_artifact:
  image: alpine
  stage: test
  script:
    - echo "Testing artifact..."
    - test -f build/index.html

unit_tests:
  image: node:22-alpine
  stage: test
  script:
    - echo "Running unit tests..."
    - npm ci
    - npm test
```

---

# 🛠️ Explanation:

| Job           | Stage | Image Used | What it Does                  |
|---------------|-------|------------|--------------------------------|
| `build_website` | build | node:22-alpine | Builds the React app |
| `test_artifact` | test | alpine | Checks if `build/index.html` exists |
| `unit_tests`    | test | node:22-alpine | Runs project unit tests |

✅ Both `test_artifact` and `unit_tests` are **in the `test` stage**  
✅ GitLab will **spin up two separate containers**:
- One with **Alpine Linux** (`test_artifact`)
- One with **Node.js 22 Alpine** (`unit_tests`)

✅ They will **start at the same time in parallel** (instead of sequential).

✅ Execution time will be **reduced** ⏱️

---

# 🎨 Visual Diagram

```plaintext
Stage: build
    ↓
[ Stage: test ]
  ┌──────────────────────┐      ┌──────────────────────┐
  │  test_artifact (Alpine)│    │  unit_tests (Node Alpine) │
  └──────────────────────┘      └──────────────────────┘
Both run at the same time ⏰
```

---

# 📢 Why Parallel Jobs Matter?
- **Fast Pipelines**: Overall execution time shrinks.
- **Better Resource Usage**: Multiple runners/containers get used efficiently.
- **Scalability**: Add more jobs without increasing total pipeline time.

---

# ⚡ Bonus Tip

If you want even *more control* over parallelism (like running same job 5 times with different data) — GitLab also supports `parallel:` keyword.  
*(That's slightly advanced — tell me if you want that example too! 🚀)*

---

# ✅ Summary

| Concept            | Meaning |
|--------------------|---------|
| Jobs in same stage  | Run in parallel |
| Different containers | Each job runs isolated |
| Faster pipelines   | Save time by running together |

---

