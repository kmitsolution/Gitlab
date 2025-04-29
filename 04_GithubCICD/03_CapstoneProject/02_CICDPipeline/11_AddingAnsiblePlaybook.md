Sure! Here's a **simple and clean example** of running an **Ansible playbook** in **GitLab CI/CD** — great for getting started.

---

## 🧾 1. **Ansible Playbook (`site.yml`)**

```yaml
---
- name: Simple ping test
  hosts: all
  tasks:
    - name: Ping the target
      ping:
```

---

## 📁 2. **Inventory File (`inventory.ini`)**

```ini
[all]
your.server.ip.here ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
```

Replace `your.server.ip.here` with your server’s IP (e.g., an EC2 instance).

---

## 🔧 3. **`.gitlab-ci.yml`**

```yaml
stages:
  - deploy

ansible-deploy:
  stage: deploy
  image: python:3.11
  before_script:
    - apt update && apt install -y sshpass
    - pip install ansible
    - mkdir -p ~/.ssh
    - echo "$ANSIBLE_PRIVATE_KEY" > ~/.ssh/id_rsa
    - chmod 600 ~/.ssh/id_rsa
    - ssh-keyscan -H your.server.ip.here >> ~/.ssh/known_hosts
  script:
    - ansible-playbook -i inventory.ini site.yml
  only:
    - main
```

---

## 🔐 4. **Set Up GitLab CI/CD Variable**

Go to **Settings → CI/CD → Variables**, and add:

| Key                   | Value (Secret)                          |
|------------------------|------------------------------------------|
| `ANSIBLE_PRIVATE_KEY` | Paste your **SSH private key** here |

---

## ✅ What It Does

- Installs Ansible inside the GitLab CI job
- Connects to your server using the SSH key
- Runs a simple `ping` test via Ansible

---

