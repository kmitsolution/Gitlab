## Troubleshoot an Error

Whenever there is an error in the pipeline then the best way to check the log to go closer to the root cause of the exception. Let's Take below example and understand better.


# 🧩 Original GitLab Pipeline YAML

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
        - echo " This code is built" > index.html
        - echo "one more line" > index.html
    artifacts:
        paths:
            - target/

testing_phase:
    image: alpine
    stage: test
    script:
        - cat target/index.html    
        - grep "built" target/index.html
        - grep "one" target/index.html
```

---

# ❌ Problem That Happened

In **`build_phase`**, these two lines were the mistake:

```bash
echo " This code is built" > index.html
echo "one more line" > index.html
```

👉 When you use `>` (single greater-than), it **overwrites** the file every time.

- First, it creates `index.html` with "This code is built"
- Then it **overwrites** and writes "one more line" — erasing the first line!!

So at the end, **`index.html` only contains**:
```
one more line
```

That’s why:
- `grep "built" target/index.html` → **fails**! (no "built" word inside)
- This causes the **testing_phase** to **fail with exit code 1** ❌.

---

# 🛠️ The Fix (Correct Way)

In the **fixed version**, you correctly changed:

```bash
echo " This code is built" >> index.html
echo "one more line" >> index.html
```

👉 `>>` (double greater-than) **appends** instead of overwriting!

So now, `index.html` will have:
```
This code is built
one more line
```

✅ Both `grep "built"` and `grep "one"` will succeed!

---

# 🧠 Important Concepts Learned Here:

| Concept         | Wrong Way  | Correct Way   |
|-----------------|------------|---------------|
| Overwrite file  | `>`         |               |
| Append to file  |             | `>>`          |
| File contents   | last `>` wins | both lines present |
| GitLab pipeline behavior | job fails if any script step exits `1` | job passes if all commands exit `0` |

---

# 📋 Final Working YAML (After Fix)

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
        - echo " This code is built" >> index.html
        - echo "one more line" >> index.html
    artifacts:
        paths:
            - target/

testing_phase:
    image: alpine
    stage: test
    script:
        - cat target/index.html    
        - grep "built" target/index.html
        - grep "one" target/index.html
```

---

# 🧠 Troubleshoot Button (AI Tool in GitLab)

- In GitLab UI, if a pipeline job fails ❌, there is a **"Troubleshoot"** button.
- Clicking it opens **GitLab Duo AI** or **explanations** about:
  - Why a job failed
  - Which command exited with error
  - Tips to fix the issue (like checking overwrite vs append, missing folders, wrong paths, etc.)

💬 **Basically: It acts like an assistant who reads the logs for you and gives hints!**

---

# 🛟 Tips for Reading Logs Patiently:

- First, read which line exactly failed (logs will show it).
- Notice if a file content is missing or wrong.
- Think: **Did I overwrite something? Did I forget to create a directory?**
- Compare the script carefully line by line.

---
