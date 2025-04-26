# First GitLab CI/CD pipeline.  
---

## 🛠️ Step 1: Create a Repository in GitLab

**Repository Name Suggestion:**  
👉 `first-gitlab-pipeline`  
or  
👉 `ci-cd-demo-pipeline`

While creating the repository, **check the option** to **"Initialize repository with a README.md"**.  
✅ This will create the repo and allow you to clone and work with it easily.

---

## 🛠️ Step 2: Create your first `.gitlab-ci.yml` file

Add this content:

```yaml
build_phase:
    image: alpine
    script:
        - echo "This is build phase"
        - mkdir /target
        - echo "<h1> This code is built" > /target/index.html
```

---

### 🧠 Explanation:

- **build_phase**: This is a **job** — basically, a task in the pipeline.
- **image: alpine**: The pipeline job uses a **lightweight Linux container** (`alpine`) to run.
- **script**:
  - It **prints** a message (`echo "This is build phase"`).
  - It **creates a directory** `/target`.
  - It **creates a file** `/target/index.html` with some HTML content inside.

---

### 🔥 What Happens After You Commit?
- GitLab **detects** the `.gitlab-ci.yml` file automatically.
- **Pipeline is triggered immediately** — no manual trigger needed.
- **Stages**: GitLab by default assigns the job to a stage called **`test`** if you don't specify one.

---

## 🛠️ Step 3: Adding more to `.gitlab-ci.yml`

Now you add another job called `testing_phase`:

```yaml
build_phase:
    image: alpine
    script:
        - echo "This is build phase"
        - mkdir /target
        - echo "<h1> This code is built" > /target/index.html

testing_phase:
    image: alpine
    script:
        - cat /target/index.html
```

---

### 🧠 Explanation:

- **testing_phase**: A **second job** is added to your pipeline.
- **script**:
  - It tries to **display** (`cat`) the content of `/target/index.html`.

---

### ❌ Problem Here:

- **/target/index.html doesn't exist in testing_phase** because:
  - Each job runs **in a fresh new container** by default.
  - `/target` created in `build_phase` **is not shared** automatically.
- **Also**, both jobs **run in parallel** because we didn’t tell GitLab to run them in sequence!

---

## 🛠️ Step 4: Fixing Issues — Proper `.gitlab-ci.yml`

Here's the corrected version:

```yaml
stages:
    - build
    - test

build_phase:
    image: alpine
    stage: build
    script:
        - echo "This is build phase"
        - mkdir target
        - cd target
        - touch index.html
        - echo "This code is built" > index.html
    artifacts:
        paths:
            - target/

testing_phase:
    image: alpine
    stage: test
    script:
        - cat target/index.html
```

---

### 🧠 Full Explanation of This Corrected Pipeline:

#### ✅ 1. **Stages**

```yaml
stages:
    - build
    - test
```
- Defines **two stages** in the pipeline.
- Now jobs **run in order**: first `build`, then `test`.
- Jobs in the same stage can run in parallel, but **stages** run sequentially.

#### ✅ 2. **Artifacts**

```yaml
artifacts:
    paths:
        - target/
```
- **Artifacts** are files or directories **saved after a job finishes**.
- GitLab **shares these artifacts** with the next stages/jobs.
- Here, `target/` directory (containing `index.html`) is **saved** and **passed** to `testing_phase`.

#### ✅ 3. **build_phase**

- Creates a directory called `target`
- Creates `index.html`
- Saves the `target/` folder as an **artifact**.

#### ✅ 4. **testing_phase**

- Downloads the saved **artifact** (target/)
- Reads (`cat`) the `index.html` and prints it.

---

# 🖼️ Visual Diagram of the Flow

```
Developer pushes code to GitLab
            ↓
GitLab sees .gitlab-ci.yml
            ↓
Pipeline starts with defined stages

    Stage 1: build
      └─ Job: build_phase
          └─ Creates target/index.html
          └─ Saves it as artifact

    Stage 2: test
      └─ Job: testing_phase
          └─ Uses artifact from build
          └─ Reads and displays index.html

            ↓
Pipeline Successful!
```

---

