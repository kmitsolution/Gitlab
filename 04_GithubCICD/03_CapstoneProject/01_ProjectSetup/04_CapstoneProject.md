Here's a step-by-step guide to **create a Spring Boot project** (as per  instructions) and push it to a **GitLab repository**:

---

### ✅ 1. Generate Spring Boot Project

Go to: [https://start.spring.io/](https://start.spring.io/)

Set the following:
- **Project**: Maven
- **Language**: Java
- **Spring Boot**: latest version (compatible with Java 21)
- **Group**: `com.capstone`
- **Artifact**: `Capstone`
- **Name**: `Capstone`
- **Package Name**: `com.capstone`
- **Java**: 21

Click **Generate**, and unzip the downloaded file.

---

### ✅ 2. Modify `pom.xml`

In the `<build>` section of `pom.xml`, add:

```xml
<build>
    <finalName>capstone</finalName>
</build>
```

---

### ✅ 3. Add `Welcome.java`

Navigate to `src/main/java/com/capstone/`, and create the file `Welcome.java` with the following content:

```java
package com.capstone;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class Welcome {

    @GetMapping
    public String getMessage() {
        return "Welcome to Spring Boot";
    }
}
```

---

### ✅ 4. Build the project

Open terminal inside the project directory and run:

```bash
mvn clean package
```

This will generate a file named `capstone.jar` in the `target/` folder.

---

### ✅ 5. Push to GitLab Repository

Assuming you've already created a **GitLab repo**, follow these steps:

```bash
cd Capstone         # navigate to the project folder
git init            # initialize git if not already
git remote add origin https://gitlab.com/YOUR_USERNAME/YOUR_REPO.git

git add .
git commit -m "Initial Spring Boot Capstone project"
git branch -M main
git push -u origin main
```

Replace `YOUR_USERNAME` and `YOUR_REPO` with your actual GitLab username and repository name.

---

