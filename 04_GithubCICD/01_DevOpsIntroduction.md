### 🔄 What is **SDLC**?

**SDLC** stands for **Software Development Life Cycle**.  
It's the **process** used to design, develop, test, and deploy software efficiently.

![image](https://github.com/user-attachments/assets/732d18f9-5241-4f35-8b59-d6956f24bb77)

![image](https://github.com/user-attachments/assets/5b6522c2-4429-40d1-a78e-36fc22c86dc9)

#### Common Phases of SDLC:
1. **Planning** – What problem are we solving?
2. **Requirements Analysis** – What does the system need to do?
3. **Design** – How will it be built?
4. **Implementation (Coding)** – Actually building it.
5. **Testing** – Is it working correctly?
6. **Deployment** – Releasing it to users.
7. **Maintenance** – Fixing bugs or updating features after release.

---

### ⚙️ What is **DevOps**?

![image](https://github.com/user-attachments/assets/3f092bf4-97f3-4cdb-b366-1f7940cffc25)


**DevOps** is a culture and set of practices that brings **Development** and **Operations** teams together to:
- Automate processes
- Improve software delivery speed
- Ensure better collaboration

DevOps fits **across the SDLC** — especially in the coding, testing, deployment, and maintenance phases.

Great question! Let’s break it down simply:

---

### ⚙️ What is **CI** (Continuous Integration)?

**CI = Automatically integrating code changes**

- Developers frequently push small code changes to a shared repository (like GitLab).
- Each push triggers:
  - **Builds** (compile or package the code)
  - **Tests** (unit tests, linting, etc.)
- Goal: Catch bugs early, keep the main branch always working.

✅ Benefits:
- Faster feedback on code quality  
- Fewer merge conflicts  
- Improved collaboration

---

### 🚀 What is **CD** (Continuous Delivery / Continuous Deployment)?

**CD = Automatically delivering/deploying code after CI**

There are **two types of CD**:

1. **Continuous Delivery**  
   - Code is automatically tested and **ready to deploy**  
   - Manual approval may be needed for production

2. **Continuous Deployment**  
   - Code is **automatically deployed to production** if all tests pass  
   - No manual step at all

✅ Benefits:
- Faster release cycles  
- Less human error  
- Quick rollback if needed

---

### 🔁 CI/CD in DevOps Context

DevOps focuses on **automation** and **collaboration**, and CI/CD is the **engine** that powers it.

```
  Code → Git Push → CI → CD → Users
                ↘ Build/Test ↘ Deploy
```

CI/CD helps deliver reliable software **faster and safer**.



---

### 🔁 GitLab CI/CD in this Context

**GitLab CI/CD** is a DevOps tool that **automates** parts of SDLC:
- **CI** (Continuous Integration): Automatically builds & tests code when changes are made.
- **CD** (Continuous Delivery/Deployment): Automatically delivers or deploys new code to staging/production.

So in essence:
> GitLab CI/CD = Automation tool in DevOps = Used to speed up SDLC stages

---

### 🔗 Relationship Summary

- **SDLC** is the full life cycle of software.
- **DevOps** is a practice to make that cycle faster and smoother.
- **GitLab CI/CD** is a tool used in DevOps to automate development, testing, and deployment — helping implement DevOps in the SDLC.

---

### 🖼️ Diagram to Understand the Flow

Here's a simple diagram showing the relationship visually:

```
         Software Development Life Cycle (SDLC)
     ┌────────┬────────────┬─────────┬─────────┬────────┬────────────┐
     │Planning│Requirement │ Design  │ Coding  │Testing │ Deployment │
     └────────┴────────────┴─────────┴─────────┴────────┴────────────┘
                                          ↓
                                    ┌────────────┐
                                    │   DevOps   │ <────── Culture & Practices
                                    └────┬───────┘
                                         ↓
                          ┌─────────────────────────────┐
                          │    GitLab CI/CD Pipeline     │
                          ├────────┬────────┬────────────┤
                          │ Build  │  Test  │ Deploy     │
                          └────────┴────────┴────────────┘
```

