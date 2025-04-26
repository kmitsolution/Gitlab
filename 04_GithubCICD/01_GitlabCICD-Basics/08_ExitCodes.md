✅ **Exit Codes in GitLab Pipelines** ✅  
Let’s dive in, with examples and clear explanation!

---

# 🛑 What is an Exit Code?

- When a command finishes running inside a shell (like Bash), it returns a **number** called an **exit code**.
- **Exit code = 0** → ✅ **SUCCESS** (everything is fine)
- **Exit code ≠ 0** → ❌ **FAILURE** (something went wrong)

In Linux/Unix systems (and GitLab pipelines),  
**Exit codes are always between `0` and `255`**.

---

# 🛠️ How GitLab Handles Exit Codes

- **Every script line** you write inside `.gitlab-ci.yml` is executed.
- If **any** command **returns a non-zero exit code**, the **job will fail immediately**.
- GitLab expects **exit code 0** for success!

---

# 📜 Example to Show How Exit Codes Affect Pipeline

```yaml
stages:
  - build

build_phase:
  image: alpine
  stage: build
  script:
    - echo "Start building"
    - exit 1
    - echo "This line will never run"
```

---

### 🔥 What happens here?

1. GitLab starts the **`build_phase`**.
2. `echo "Start building"` prints normally (exit 0 ✅).
3. `exit 1` is executed:
   - Exit code is `1` ❌
   - GitLab **immediately stops the job**!
   - The next line (`echo "This line will never run"`) is **NOT executed**.
4. The job is marked **FAILED** in GitLab UI.

**Logs will show something like:**
```
Running with gitlab-runner...
Executing "script" stage of the job
$ echo "Start building"
Start building
$ exit 1
ERROR: Job failed: exit code 1
```

---

# 🧠 Common Exit Codes (Meaning)

| Exit Code | Meaning                    |
|-----------|-----------------------------|
| 0         | Success                     |
| 1         | General error               |
| 2         | Misuse of shell builtins     |
| 127       | Command not found            |
| 126       | Command found but not executable |
| 130       | Script terminated by Ctrl+C  |

*(These are standard Linux shell codes.)*

---

# 🧪 Example with Proper Flow (No error)

```yaml
stages:
  - build

build_phase:
  image: alpine
  stage: build
  script:
    - echo "Start building"
    - exit 0
    - echo "This line will run"
```

✅ Here, `exit 0` is **good** — pipeline continues, job **passes**.

---

# 💬 Important Tips for GitLab Pipelines:

- **Always aim for exit 0** unless you intentionally want to fail the job.
- If using **custom scripts**, handle errors gracefully, like:
  ```bash
  my_script.sh || exit 1
  ```
- If you want to **ignore** a failure for some reason (rare), you can use:
  ```yaml
  script:
    - some-command || true
  ```

---

# 📚 Quick Visual Summary

```plaintext
┌──────────────┐
│  Run Script  │
└──────┬───────┘
       ↓
┌───────────────────────┐
│ Get Exit Code (0-255)  │
└──────┬───────┬─────────┘
       ↓       ↓
   Exit 0    Exit ≠ 0
    ✅         ❌
  Success     Fail Job
```

---

# 🛎️ Final Important Points:
- Exit codes are critical to understanding **why a job passed or failed**.
- Pipeline looks **only at exit codes** of each command step.
- Range: **0–255** (anything beyond wraps around)
- `exit 1`, `exit 2`, `exit 127` → all are treated as **errors**.

---

Let’s do a **small experiment** to understand **exit code handling** smartly inside a GitLab pipeline.

---

# 🧪 Mini Experiment: "Smart" Handling of Exit Codes in GitLab CI/CD

We will **simulate a command failure**, but **still control** whether the job should pass or fail **ourselves**. 😎

---

### 🔥 Pipeline Example

```yaml
stages:
  - experiment

smart_job:
  image: alpine
  stage: experiment
  script:
    - echo "Trying risky command..."
    - ls not_a_real_folder || echo "Folder not found, but we continue!"
    - echo "Manually checking command success"
    - if [ -d "not_a_real_folder" ]; then echo "Folder exists"; else echo "Folder missing, still OK"; fi
    - exit 0
```

---

### 🧠 What’s Happening Step-by-Step:

1. `echo "Trying risky command..."` → prints message.
2. `ls not_a_real_folder` → ❌ **fails** (folder doesn't exist), but because of `|| echo ...`, it **doesn't stop the job**!
3. `if` block checks if folder exists:
   - It prints a **custom safe message** if not found.
4. `exit 0` → manually **forces the exit code to be 0** —> **job success!**

✅ Final result: **Pipeline will still pass!**

---

# 🎯 Why This Is "Smart"?

- **Normally**, if `ls not_a_real_folder` fails, GitLab would stop the job ❌.
- But with `|| echo "some fallback"`, we tell the runner:
  > "If it fails, that's fine — just continue."
- Then at the end, we manually **decide** the exit behavior.
- **You are in full control.** 🧠

---

# 📋 Another Example: **Fail the Job Intentionally on Purpose**

Sometimes you want to **catch an error** and **manually fail** if needed:

```yaml
stages:
  - experiment

fail_on_purpose:
  image: alpine
  stage: experiment
  script:
    - echo "Checking if file exists..."
    - if [ ! -f important_file.txt ]; then echo "Missing important file! Failing..." && exit 1; fi
    - echo "All good!"
```

✅ This will **fail** the job if `important_file.txt` is missing.

---

# 🎨 Visual Flow

```plaintext
Run risky command
       ↓
If it fails, fallback action (|| echo "...")
       ↓
Custom checking (if conditions)
       ↓
Manual exit code (exit 0 or exit 1)
       ↓
Pipeline status (pass or fail)
```

---

# 🚀 Final Tips:

- Use `|| true` if you want to **ignore** failures temporarily.
- Use `|| exit 1` if you want to **force fail** based on conditions.
- Always **think about exit codes** when writing any `script:` steps in `.gitlab-ci.yml`.

---

