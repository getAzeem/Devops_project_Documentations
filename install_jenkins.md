# Jenkins and Java Installation Script

This script automates the installation of Java and Jenkins on various Linux distributions.

---

## **Script Workflow**

1. **Check for Root Privileges**
   - Ensures the script is executed with root or sudo privileges to allow package installation and system configurations.

2. **Detect Operating System**
   - Identifies the Linux distribution (Ubuntu/Debian, CentOS/RHEL, Fedora, or Amazon Linux) to run the appropriate installation commands.

3. **Java Installation**
   - Prompts the user to select a Java version and installs the chosen version based on the detected OS.

4. **Jenkins Installation**
   - Prompts the user for a Jenkins version, sets up the Jenkins repository, and installs the specified version.

5. **Post-Installation Instructions**
   - Displays access details and additional instructions for using Jenkins.

---

## **Functions Explained**

### `detect_os`
**Purpose:** Detects the Linux operating system.

**Logic:**
- Reads `/etc/os-release` to identify the OS.
- Sets a variable `$os` based on the `ID` field in the file.
- Supports Debian-based systems (Ubuntu, Debian), RHEL-based systems (RHEL, CentOS), Fedora, and Amazon Linux.
- Exits with an error message if the OS is unsupported.

---

### `get_java_version`
**Purpose:** Prompts the user to select a Java version.

**Logic:**
- Displays a menu with two Java LTS versions (17 and 21).
- Reads the user's choice and assigns the corresponding version to a variable `$java_version`.
- Ensures valid input using a `while` loop.

---

### `install_java`
**Purpose:** Installs the selected Java version.

**Logic:**
- Takes the selected Java version as input.
- Executes the appropriate package management commands (`apt`, `yum`, `amazon-linux-extras`) based on the detected OS.
- Runs the `java -version` command to verify installation.

---

### `get_jenkins_version`
**Purpose:** Prompts the user to input the desired Jenkins version.

**Logic:**
- Uses a `while` loop to ensure the version format is valid (e.g., `2.401.1`).
- Returns the entered version for installation.

---

### `install_jenkins`
**Purpose:** Installs the specified Jenkins version.

**Logic:**
- Configures the Jenkins repository based on the OS.
  - **Debian-based systems:** Adds Jenkins' GPG key and repository to `/etc/apt/sources.list.d`.
  - **RHEL-based systems:** Adds Jenkins' repository file to `/etc/yum.repos.d` and imports the GPG key.
- Installs the specified Jenkins version using the system's package manager.
- Starts and enables the Jenkins service with `systemctl`.

---

### `show_instructions`
**Purpose:** Displays instructions for accessing Jenkins.

**Logic:**
- Displays installed Java and Jenkins versions.
- Provides the URL for Jenkins (`http://localhost:8080`) and instructions to retrieve the initial admin password.

---

### `main`
**Purpose:** Orchestrates the script execution.

**Logic:**
1. Checks if the script is run with root privileges.
2. Calls `detect_os` to identify the operating system.
3. Calls `get_java_version` and installs the selected Java version using `install_java`.
4. Calls `get_jenkins_version` and installs Jenkins using `install_jenkins`.
5. Displays post-installation instructions via `show_instructions`.

---

## **How to Use the Script**

1. **Download the Script**
   - Save the script to a file (e.g., `install_jenkins.sh`).

2. **Make the Script Executable**
   ```bash
   chmod +x install_jenkins.sh
   ```

3. **Run the Script with Root Privileges**
   ```bash
   sudo ./install_jenkins.sh
   ```

4. **Follow the Prompts**
   - Select the desired Java version.
   - Enter the Jenkins version to install.

5. **Access Jenkins**
   - Open a browser and navigate to `http://localhost:8080`.


## **Notes**
- **Supported Operating Systems:**
  - Ubuntu/Debian
  - CentOS/RHEL
  - Fedora
  - Amazon Linux
- Ensure your system has internet access to download necessary packages.
- Customize the script as needed for other OS or software versions.

