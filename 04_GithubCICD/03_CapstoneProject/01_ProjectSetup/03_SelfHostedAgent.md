
#  GitLab Runner Setup (Self-Hosted Agent)

> ✅ Goal: Install and configure a self-hosted GitLab Runner on the `GitLab-Runner-VM` for the `Capstone` project under the `capstonegroup` group.

---

## 🔐 Step 1: Create a Personal Access Token (PAT)

1. Go to your GitLab profile → **Preferences**
2. Navigate to **Access Tokens**
3. Create a new token with the following:
   - **Name**: `capstone-pat`
   - **Scopes/Permissions**:
     - `api`
     - `read_user`
     - `read_repository`
     - `write_repository`
4. Save the token (example used: `glpat-bUfx6exc8_yao4LN2z7Rx`) – this is used for Git operations or GitLab API if needed.

---

## 🧭 Step 2: Create a New Project Runner

1. Go to your project: `capstonegroup/Capstone`
2. Navigate to:
   - **Settings → CI/CD → Runners**
   - Expand the **"Runners"** section
   - Click **"New project runner"**
3. On the runner setup screen:
   - **Tags:** `capstone`
   - Click **Create Runner**

---

## 🖥️ Step 3: Install GitLab Runner on GitLab-Runner Server (Linux VM)

SSH into the GitLab-Runner server and run the following:

```bash
# Download the binary for GitLab Runner
sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64

# Give it executable permissions
sudo chmod +x /usr/local/bin/gitlab-runner

# Create a user for the runner
sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash

# Install and enable the runner service
sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
sudo gitlab-runner start
```

---

## 📝 Step 4: Register the GitLab Runner

1. Still in the GitLab runner setup page (after clicking **Create Runner**), click:
   - **Linux**
   - **"How do I install GitLab Runner?"** for registration instructions

2. On the runner server, run:

```bash
sudo gitlab-runner register
```

3. Follow the prompts:
   - **GitLab URL:** `https://gitlab.com/`
   - **Registration token:** (copy from the project runner registration page)
   - **Description:** `Capstone Runner`
   - **Tags:** `capstone`
   - **Executor:** `shell` (or `docker` if preferred)
   - Complete the registration process

---

## ✅ Verify Runner

To verify the runner is connected:
1. Go to **Project → Settings → CI/CD → Runners**
2. You should see the **Capstone Runner** listed under **Available Runners**.

