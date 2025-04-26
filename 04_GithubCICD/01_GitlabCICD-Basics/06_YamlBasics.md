# 📄 YAML Basics

### 1. **Key-Value Pairs**

- The most basic building block in YAML.
- A **key** is like a name, and a **value** is its content.

**Example:**

```yaml
language: python
version: 3.8
framework: django
```

**Meaning:**  
- `language` = `python`
- `version` = `3.8`
- `framework` = `django`

---

### 2. **Collection Objects**

There are **two types** of collections:

---

#### 📋 a) List (Sequence)

- Represented by **dash `-`** in YAML.
- It's an **ordered** collection (like an array in other languages).

**Example:**

```yaml
stages:
  - build
  - test
  - deploy
```

**Meaning:**  
Stages are a list: `["build", "test", "deploy"]`

---

#### 📦 b) Dictionary (Mapping)

- A **key** that contains **another set of keys and values** (nested objects).

**Example:**

```yaml
build_job:
  image: alpine
  script:
    - echo "Building the code"
    - mkdir target
```

**Meaning:**  
`build_job` is a dictionary that contains:
- `image` = `alpine`
- `script` = list of commands

---

# 🔗 Relating YAML Basics to GitLab CI/CD

The `.gitlab-ci.yml` file **IS A YAML FILE**.  
It uses **key-value pairs, lists, and dictionaries** to define pipelines.

👉 Let's match examples:

---

### **Key-Value in GitLab CI/CD**

```yaml
image: alpine
```
- `image` is the key.
- `alpine` is the value.
- **Meaning:** Use `alpine` Linux as the container for the job.

---

### **List in GitLab CI/CD**

```yaml
stages:
  - build
  - test
  - deploy
```
- `stages` key holds a **list**.
- Tells GitLab the order of pipeline stages.

---

### **Dictionary (Nested Object) in GitLab CI/CD**

```yaml
build_job:
  stage: build
  script:
    - echo "Building app"
    - mkdir build_dir
```
- `build_job` is a **dictionary** with two keys: `stage` and `script`.
- `script` itself holds a **list** of bash commands to execute.

---

# 🛠️ Mini GitLab CI/CD Example Using YAML Concepts

```yaml
# Defining stages as a list
stages:
  - build
  - test

# Defining first job using key-value and dictionaries
build_job:
  stage: build
  image: alpine
  script:
    - echo "Building project"
    - mkdir target

# Defining second job
test_job:
  stage: test
  image: alpine
  script:
    - echo "Testing project"
    - ls target/
```

**Breakdown:**
- `stages:` is a **list**.
- `build_job:` and `test_job:` are **dictionaries**.
- Inside each job, we use **key-value pairs** (`image`, `stage`, `script`).
- `script:` is a **list** of commands to execute inside the container.

---

# 🎯 Quick Summary Table

| YAML Concept | How it Appears in GitLab CI/CD |
|--------------|---------------------------------|
| Key-Value    | `image: alpine`                 |
| List         | `stages: - build - test`         |
| Dictionary   | `build_job: { stage: build, ... }` |

---

