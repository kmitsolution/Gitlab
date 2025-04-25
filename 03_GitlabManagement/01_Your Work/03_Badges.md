# Badge in GitLab

A **badge** is a small, visual label (usually with an icon and text) that displays **real-time status information** about your project.

Think of it as a little **status tag** — like “✅ Build Passing”, “🔒 License: MIT”, “📦 Version: 1.2.3”.

These badges are typically shown in the **project README** and/or at the top of the **project homepage**.

---

## 🎯 **Purpose of Badges**

Badges are used to **quickly communicate project health or metadata**, such as:

| Badge Type       | Purpose                                      |
|------------------|----------------------------------------------|
| ✅ **Pipeline Status**  | Shows if the latest CI/CD pipeline passed or failed |
| 📦 **Latest Version**   | Shows the current released version (for packages, apps) |
| 🔒 **License**          | Shows what license the repo uses (MIT, GPL, etc.) |
| 📄 **Docs Status**      | Shows if documentation builds correctly |
| 🌐 **Coverage**         | Displays test coverage percentage |
| 🧪 **Custom Metrics**   | You can create custom badges with any URL/data |

---

## 🔧 How to Add Badges in GitLab

### ✅ Step 1: Go to Project Settings

1. Open your GitLab project
2. Go to **Settings ➝ General**
3. Scroll to **“Badges”** section

### ✅ Step 2: Add a New Badge

Click **“Add badge”** and fill out:

| Field | What It Does |
|-------|---------------|
| **Name** | Badge label (e.g., `Pipeline`, `Coverage`) |
| **Link** | Where the badge redirects (e.g., CI page) |
| **Image URL** | The badge image (static or dynamic) |
| **Badge type** | Static or dynamic |

---

## 💡 Example: Pipeline Status Badge

GitLab **automatically provides** a pipeline badge:

- **Image URL:**
  ```
  https://gitlab.com/<namespace>/<project>/badges/<branch>/pipeline.svg
  ```
- **Link URL:**
  ```
  https://gitlab.com/<namespace>/<project>/pipelines
  ```

Replace `<namespace>`, `<project>`, and `<branch>` with your info.

You can then add the badge to your **README.md** like this:

```markdown
![Pipeline Status](https://gitlab.com/mygroup/myproject/badges/main/pipeline.svg)
```

---

## 🧪 Other Examples (Using Shields.io)

You can also create custom badges using [Shields.io](https://shields.io):

```markdown
![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)
```

---

## 📌 Use Case Summary

- ✅ Show build/test status (CI/CD)
- 📈 Communicate quality (coverage, linting, etc.)
- 🔐 Clarify open source license
- 🔍 Provide quick links to docs or releases
- 🧩 Display metrics from external tools (Docker Hub, SonarQube, etc.)

---

