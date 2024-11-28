# Backup Script Documentation

## Overview
This Bash script automates the process of creating backups from a source directory to a destination directory. It supports both compressed and uncompressed backups and logs all operations. Additionally, it performs cleanup by removing backups older than 7 days from the destination directory.

---

## Script Usage

### Syntax
```bash
./backup.sh <SOURCE_DIR> <DEST_DIR> <COMPRESS>
```

### Parameters
- **`SOURCE_DIR`**: The directory to be backed up (must exist).
- **`DEST_DIR`**: The destination directory where backups will be stored (will be created if it doesn't exist).
- **`COMPRESS`**: Specifies whether the backup should be compressed. Acceptable values are:
  - `yes`: Create a compressed `.tar.gz` backup.
  - `no`: Create an uncompressed directory backup.

---

## Features

1. **Logging**:
   - All operations are logged in `/var/log/backup.log`.
   - Logs include timestamps and status messages for easy troubleshooting.

2. **Compression**:
   - When `COMPRESS` is set to `yes`, the script creates a `.tar.gz` file of the source directory.

3. **Uncompressed Backup**:
   - When `COMPRESS` is set to `no`, the source directory is copied recursively to the destination directory.

4. **Automatic Cleanup**:
   - Backups older than 7 days are automatically removed from the destination directory to save space.

5. **Error Handling**:
   - Validates input parameters and ensures the existence of the source directory.
   - Handles failures during backup creation and logs errors.

---

## Detailed Workflow

### 1. **Input Validation**:
   - Ensures both `SOURCE_DIR` and `DEST_DIR` are provided.
   - Exits with an error message if any parameter is missing or invalid.

### 2. **Source Directory Check**:
   - Confirms the existence of the source directory.
   - Logs an error and exits if the directory is missing.

### 3. **Destination Directory Setup**:
   - Creates the destination directory if it doesn’t already exist.

### 4. **Backup Creation**:
   - **Compressed Backup**:
     - Creates a `.tar.gz` archive of the source directory.
     - Logs success or failure messages.
   - **Uncompressed Backup**:
     - Recursively copies the source directory to the destination.
     - Logs success or failure messages.

### 5. **Cleanup**:
   - Removes backups in the destination directory older than 7 days.
   - Logs the cleanup operation.

---


## Log File
- **Location**: `/var/log/backup.log`

---

## Notes
- The script requires appropriate permissions to:
  - Write to `/var/log/backup.log`.
  - Create directories and files in `DEST_DIR`.
- Ensure sufficient storage space in the destination directory.

---

## Troubleshooting

1. **Permission Denied Errors**:
   - Ensure the script is executed with sufficient privileges (e.g., `sudo` if necessary).

2. **Backup Creation Fails**:
   - Verify the source directory exists and is accessible.
   - Check the log file (`/var/log/backup.log`) for detailed error messages.

3. **Logs Not Written**:
   - Confirm write permissions for `/var/log/backup.log`.



