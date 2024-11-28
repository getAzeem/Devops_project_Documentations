
# Jenkins Pipeline: Maven Project Build, Test, and Deployment

This Jenkins pipeline automates the process of building, testing, packaging, and publishing a Maven-based Java application. It includes parameters for flexibility, robust error handling, and automatic scheduling for efficiency.

---

## **Pipeline Overview**

### **Agent**
- **Agent**: The pipeline runs on any available Jenkins agent that matches the `any` label.

### **Tools**
- **Maven**: The pipeline uses a pre-configured Maven installation, referenced by the tool name `MAVEN_HOME`. This ensures that Maven commands executed in the pipeline use a consistent and pre-verified configuration.

### **Parameters**
- **`BRANCH_NAME`**: A string parameter allowing the user to specify the Git branch to build. The default value is set to `main`, but it can be overridden when triggering the pipeline manually.

### **Options**
- **Timeout**: The pipeline is configured to fail automatically if any build stage exceeds 1 minute, preventing long-running jobs from consuming resources unnecessarily.

---

## **Pipeline Stages: Detailed Explanation**

### **1. Cleanup Workspace**
- **Purpose**: Deletes all files in the workspace to ensure that each build starts from a clean state. This eliminates the risk of leftover files from previous builds affecting the current build.
- **Command**: `cleanWs()`

### **2. Clone Repository**
- **Purpose**: Checks out the specified branch from the GitHub repository.
- **Command**: 
    ```
    git clone --branch ${params.BRANCH_NAME} https://github.com/Aseemakram19/maven-app.git
    ```
- **Details**: The branch to clone is defined dynamically through the `BRANCH_NAME` parameter, allowing flexibility for multi-branch builds.

### **3. Clean Project**
- **Purpose**: Cleans up any previously compiled artifacts and other generated files from the Maven project directory.
- **Command**: 
    ```
    mvn clean -f maven-app
    ```
- **Details**: The `-f` flag specifies the location of the Maven project (`maven-app`). This ensures the correct project is cleaned.

### **4. Install Dependencies**
- **Purpose**: Installs all dependencies required for the project, including libraries and plugins, as defined in the `pom.xml` file.
- **Command**: 
    ```
    mvn install -f maven-app
    ```
- **Details**: This step ensures that the local Maven repository is populated with all necessary dependencies.

### **5. Run Tests**
- **Purpose**: Executes unit tests defined in the Maven project to validate the functionality of the code.
- **Command**: 
    ```
    mvn test -f maven-app
    ```
- **Details**: This step reports any test failures, ensuring that only code passing all tests proceeds to the next stage.

### **6. Package Application**
- **Purpose**: Packages the application into a deployable format, such as a JAR file.
- **Command**: 
    ```
    mvn package -f maven-app
    ```
- **Details**: The generated artifact is stored in the `target/` directory within the project.

### **7. Publish Artifacts**
- **Purpose**: Archives the packaged JAR file as a Jenkins build artifact, making it available for download or further use in subsequent pipelines.
- **Command**: 
    ```
    archiveArtifacts allowEmptyArchive: true, artifacts: 'maven-app/target/*.jar', fingerprint: true
    ```
- **Details**: This step ensures the artifact is recorded with a fingerprint for tracking across builds.

---

## **SCM and Build Scheduling**

### **1. Poll SCM (`H/5 * * * *`)**
- **Purpose**: Jenkins checks the Git repository every 5 minutes for changes. If changes are detected, the pipeline triggers automatically.
- **Benefits**: Automates builds based on code changes, ensuring the pipeline always runs on the latest version of the branch.

### **2. Build Periodically (`H * * * *`)**
- **Purpose**: Triggers the pipeline automatically once every hour, regardless of repository changes.
- **Benefits**: Ensures regular builds to monitor the stability of the project, even if no code changes occur.

---

## **Key Features and Advantages**

1. **Workspace Cleanup**: Guarantees isolation between builds by starting each job with a fresh workspace.
2. **Dynamic Branch Selection**: Enables flexibility in building any branch through the `BRANCH_NAME` parameter.
3. **Comprehensive Maven Workflow**: Covers all essential Maven phases: cleaning, dependency installation, testing, and packaging.
4. **Artifact Management**: Archives and fingerprints build artifacts, making them accessible for debugging or deployment.
5. **Automated Scheduling**: Combines SCM polling and periodic builds to maintain up-to-date and stable builds.

---

## **Error Handling and Best Practices**

- **Timeouts**: Ensures resource efficiency by failing jobs that exceed 1 minute.
- **Fail-Fast Strategy**: If tests fail or the repository cannot be cloned, the pipeline stops immediately, preventing wasted time on subsequent stages.
- **Artifact Retention**: Archives artifacts securely for auditing and downstream usage.

---
