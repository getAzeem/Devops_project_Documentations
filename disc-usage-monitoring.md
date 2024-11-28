# Jenkins Pipeline: Disk Usage Monitoring and Alert

This pipeline is designed to monitor disk usage on a specific agent node (`Slave_01`), check if any disk partitions exceed a specified threshold, and send email notifications if required. Below is a detailed explanation of its functionality and behavior.

---

## **Pipeline Overview**

### **Agent**
- The pipeline runs on the agent node labeled `Slave_01`.

### **Options**
1. **Build Discarder**: Retains only the last 10 builds to save storage space.
2. **Timeout**: Ensures the pipeline stops if it takes more than 5 minutes.

### **Environment Variables**
- **`THRESHOLD`**: Set to 80, representing the maximum allowable disk usage percentage before triggering an alert.
- **`EMAIL_TO`**: Set to `remembermeasazeem@gmail.com`, the recipient of email notifications.

---

## **Pipeline Stages**

### **Stage: Check Disk Usage**
1. **Script Execution**:
   - A Bash script (`check_disk.sh`) is created dynamically within the pipeline to:
     - Collect disk usage data using `df -H`.
     - Check if any partition exceeds the defined threshold.
     - Save a full disk usage report (`full_report.txt`) and an alert status (`alert_status`).

2. **Execution Flow**:
   - The script checks all partitions, excluding `tmpfs`, `cdrom`, and `devtmpfs`.
   - If disk usage exceeds the threshold, it logs a warning and updates the `alert_status` file to indicate an alert.
   - If no issues are found, the pipeline proceeds normally.

3. **Failure Management**:
   - If any partition exceeds the threshold, the pipeline marks the build as **UNSTABLE** and terminates with an error message.

---

## **Post Actions**

### **Always Block**
- **Email Notification**:
  - Sends an email to the specified recipient with:
    - Job name, build number, status, and disk usage report.
    - Log attachment for troubleshooting.
  - If email sending fails, the pipeline logs the failure but continues execution.

---

## **Key Features of the Pipeline**

1. **Disk Monitoring**: Dynamically generates and executes a script to check disk usage on the assigned agent node.
2. **Threshold Enforcement**: Compares disk usage against a specified threshold (80%) and identifies partitions at risk.
3. **Detailed Reporting**: Logs full disk usage details and alerts for partitions exceeding the threshold.
4. **Failure Handling**: Marks the build as `UNSTABLE` or `FAILURE` based on the disk usage results, ensuring timely notification.
5. **Automated Email Alerts**: Sends a detailed email notification to the specified recipient with the job's status and disk usage report.

---

## **Pipeline Code**
<!-- 
```groovy
pipeline {
    agent {
        label 'Slave_01'
    }
    
    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 5, unit: 'MINUTES')
    }
    
    environment {
        THRESHOLD = '80'
        EMAIL_TO = 'remembermeasazeem@gmail.com'
    }
    
    stages {
        stage('Check Disk Usage') {
            steps {
                script {
                    try {
                        echo "Starting disk usage check..."
                        
                        writeFile file: 'check_disk.sh', text: '''#!/bin/bash
                            echo "Checking disk usage on $(hostname)..."
                            
                            df -H | grep -vE '^Filesystem|tmpfs|cdrom|devtmpfs' > disk_usage.txt
                            
                            alert=0
                            
                            while read -r line; do
                                usage=$(echo "$line" | awk '{print $5}' | cut -d'%' -f1)
                                partition=$(echo "$line" | awk '{print $1}')
                                mountpoint=$(echo "$line" | awk '{print $6}')
                                
                                echo "Checking partition: $partition, Usage: $usage%"
                                
                                if [ "$usage" -ge "${THRESHOLD}" ]; then
                                    echo "WARNING: $partition ($mountpoint) is at $usage%"
                                    alert=1
                                fi
                            done < disk_usage.txt
                            
                            df -H > full_report.txt
                            echo "$alert" > alert_status
                        '''
                        
                        sh 'chmod +x check_disk.sh'
                        sh './check_disk.sh'
                        
                        def diskReport = readFile('full_report.txt')
                        echo "Disk Usage Report:\n${diskReport}"
                        
                        def alertStatus = readFile('alert_status').trim()
                        echo "Alert Status: ${alertStatus}"
                        
                        if (alertStatus == '1') {
                            currentBuild.result = 'UNSTABLE'
                            error('Disk usage threshold exceeded')
                        }
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        echo "Error occurred: ${e.getMessage()}"
                        throw e
                    }
                }
            }
        }
    }
    
    post {
        always {
            script {
                echo "Attempting to send email notification..."
                
                def emailBody = """
                    Job: ${env.JOB_NAME}
                    Build Number: ${env.BUILD_NUMBER}
                    Status: ${currentBuild.result ?: 'SUCCESS'}
                    
                    Disk Usage Report:
                    ${readFile('full_report.txt')}
                    
                    Build URL: ${env.BUILD_URL}
                """
                
                try {
                    emailext (
                        subject: "Jenkins Build ${env.JOB_NAME} - ${currentBuild.result ?: 'SUCCESS'}",
                        body: emailBody,
                        to: env.EMAIL_TO,
                        attachLog: true,
                        mimeType: 'text/plain',
                        compressLog: true
                    )
                    echo "Email sent successfully"
                } catch (Exception e) {
                    echo "Failed to send email: ${e.getMessage()}"
                }
            }
        }
    }
} -->
