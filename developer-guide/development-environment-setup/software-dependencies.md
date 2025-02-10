## Software Dependencies

### **1. Operating System:**

The development environment for a Spring Boot 3 and Angular 16 application can be set up on any modern operating system. Ensure that your system meets the following requirements:

- **Windows 10/11 (64-bit)**: Suitable for most development setups.
- **macOS (Big Sur or later)**: Offers great compatibility with open-source tooling and a Unix-based system.
- **Linux (Ubuntu 20.04+, Fedora, etc.)**: Popular among developers for its performance, flexibility, and ease of use in programming environments.

---

### **2. Java Development Kit (JDK):**

Spring Boot 3 requires **Java 17** (LTS version) as it offers long-term support and compatibility.

- **Version**: Java 17 (LTS).
- **Installation Guide**:
  - [**Oracle JDK**](https://www.oracle.com/java/technologies/javase-downloads.html): Official JDK from Oracle with licensing and support.
  - [**OpenJDK**](https://jdk.java.net/17/): Free and open-source implementation.
- **Verify Installation**: Once installed, verify by running the following command:
  ```bash
  java -version
  ```
  It should display Java 17.

---

### **3. Node.js and npm (for Angular 16):**

Node.js is essential for Angular development, as it powers the Angular CLI, package management, and builds.

- **Version**: Node.js 18+ (for Angular 16+).
- **Installation Guide**:
  - [**Node.js Official Downloads**](https://nodejs.org/en/download/): Download the LTS version for the best stability and support.
- **Verify Installation**: After installing, verify by running:
  ```bash
  node --version
  npm --version
  ```

---

### **4. Angular CLI:**

The Angular CLI helps you scaffold and manage Angular applications.

- **Version**: Angular CLI 16.x.
- **Installation Command**: Install Angular CLI globally via npm:
  ```bash
  npm install -g @angular/cli@16
  ```
- **Verify Installation**: Run `ng version` to confirm the CLI is properly installed.

---

### **5. Spring Boot:**

Spring Boot simplifies the development of Java-based enterprise applications by providing pre-configured templates and reducing boilerplate.

- **Version**: Spring Boot 3.x.
- **Build Tool**: Maven (preferred) or Gradle.
- **Installation Command (Maven)**: Maven is a build automation tool used for managing dependencies and packaging your Spring Boot project.
  - [**Maven Installation Guide**](https://maven.apache.org/install.html).
- **Verify Installation**: After installing Maven, verify with:
  ```bash
  mvn -version
  ```

---

### **6. Development Environment:**

You’ll need integrated development environments (IDEs) that support Java and TypeScript for backend and frontend development, respectively.

- **IDE for Backend (Spring Boot)**:
  - [**IntelliJ IDEA**](https://www.jetbrains.com/idea/download/): Great for Java-based development and Spring Boot. It has dedicated support for Spring.
  - [**Eclipse IDE**](https://www.eclipse.org/downloads/): A popular open-source IDE with Spring Boot support.
  - [**Spring Tool Suite (STS)**](https://spring.io/tools): A specialized IDE tailored for Spring applications.
- **IDE for Frontend (Angular)**:
  - [**Visual Studio Code**](https://code.visualstudio.com/download/): A lightweight and highly customizable editor for TypeScript and Angular development.
  - [**WebStorm**](https://www.jetbrains.com/webstorm/download/): A feature-rich IDE with built-in Angular support.

---

### **7. Docker and Docker Compose (Optional):**

Docker and Docker Compose are only required if you want to set up dummy data locally using the `amrit-devops` repository. This setup includes pre-configured databases (MySQL and MongoDB) and Redis, so you **do not need to manually install MySQL, MongoDB, or Redis** (steps 8, 9, and 10) if using Docker.

- **Docker**:
  - **Version**: Docker 20.10+.
  - [**Install Docker**](https://docs.docker.com/get-docker/).
  - **Verify Installation**:
    ```bash
    docker --version
    ```
- **Docker Compose**:
  - **Version**: Docker Compose 2.1+.
  - [**Install Docker Compose**](https://docs.docker.com/compose/install/).
  - **Verify Installation**:
    ```bash
    docker-compose --version
    ```

---

### **8. Database (Optional):**

If you are **not using Docker**, the AMRIT platform requires a database. We use **MySQL** and **MongoDB**.

- **MySQL**:
  - **Version**: MySQL 8+.
  - [**Download MySQL**](https://dev.mysql.com/downloads/).
  - After installation, use MySQL Workbench or the command line to manage your database.
  - Configure your Spring Boot application with the required database connection properties in the `application.properties` file.
- **MongoDB**:
  - **Version**: MongoDB 6.0+.
  - [**Download MongoDB**](https://www.mongodb.com/try/download/community).
  - After installation, use MongoDB Compass or the command line to manage your database.
  - Configure your Spring Boot application with the required MongoDB connection properties in the `application.properties` file.

---

### **9. Caching (Optional):**

If you are **not using Docker**, Redis is used for caching to improve performance.

- **Redis**:
  - [**Download Redis**](https://redis.io/download).
  - Redis is integrated into Spring Boot via the `spring-data-redis` library.

---

### **10. Other Tools:**

- **Git**: Version control system.
  - [**Download Git**](https://git-scm.com/downloads).
  - **Verify Installation**:
    ```bash
    git --version
    ```
- **NVM (Node Version Manager)**: To manage multiple Node.js versions if needed.
  - [**Download NVM**](https://github.com/nvm-sh/nvm).
- **Browser**: The latest version of Google Chrome, Firefox, or Microsoft Edge is recommended for testing and debugging Angular apps.

---

### **11. Additional Recommendations:**

- **Memory Allocation**: Spring Boot applications, especially large ones, may require a substantial amount of memory. Allocate 2 GB or more to the JVM to avoid performance issues during development.
  ```bash
  export JAVA_OPTS="-Xms2g -Xmx4g"
  ```

---
