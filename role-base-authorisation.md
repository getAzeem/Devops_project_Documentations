# Jenkins Role-Based Access Control (RBAC) Setup Documentation

This document outlines the configuration of Jenkins RBAC using **Item Roles** and **Global Roles** to manage permissions for jobs and users.

---

## **Jobs Created**
1. **Windows Jobs**:
   - `windows-job-1`
   - `windows-job-2`
2. **Data Jobs**:
   - `data-job-1`
   - `data-job-2`

---

## **Users Created**
1. **Data Engineers**:
   - `data-engineer-1`
   - `data-engineer-2`
2. **Windows Users**:
   - `windows-user-1`
   - `windows-user-2`

---

## **Roles and Permissions**

### **Item Roles**
Four item roles were created to manage access levels for jobs:

1. **`full-data-access`**  
   Permissions:
   - Build
   - Configure
   - Read
   - Workspace  
   Applicable Jobs: `data-job-.*`

2. **`full-windows-access`**  
   Permissions:
   - Build
   - Configure
   - Read
   - Workspace  
   Applicable Jobs: `windows-job-.*`

3. **`view-data-access`**  
   Permissions:
   - Read  
   Applicable Jobs: `data-job-.*`

4. **`view-windows-access`**  
   Permissions:
   - Read  
   Applicable Jobs: `windows-job-.*`

---

### **Global Roles**
One global role was created:

1. **`employee`**  
   Permission:
   - Read under the Overall tab.

---

## **Role Assignments**

### **Global Role Assignments**
All four users (`data-engineer-1`, `data-engineer-2`, `windows-user-1`, `windows-user-2`) were assigned the `employee` role globally.  
This provides basic read access across Jenkins.

---

### **Item Role Assignments**

#### **Data Engineers**
- `data-engineer-1`:
  - **Full Access**: `full-data-access` (Build, Configure, Read, Workspace)
  - **View Access**: `view-data-access` (Read)
  - **Additional Access**: `view-windows-access` (Read)
- `data-engineer-2`:
  - **Full Access**: `full-data-access` (Build, Configure, Read, Workspace)
  - **View Access**: `view-data-access` (Read)

#### **Windows Users**
- `windows-user-1`:
  - **Full Access**: `full-windows-access` (Build, Configure, Read, Workspace)
  - **View Access**: `view-windows-access` (Read)
  - **Additional Access**: `view-data-access` (Read)
- `windows-user-2`:
  - **Full Access**: `full-windows-access` (Build, Configure, Read, Workspace)
  - **View Access**: `view-windows-access` (Read)

---

## **Resulting Access Permissions**

### **Data Engineers**
- **Full Access** to data jobs:
  - `data-job-1`
  - `data-job-2`
- **Additional Access**:
  - `data-engineer-1` can view windows jobs (`windows-job-1` and `windows-job-2`).

### **Windows Users**
- **Full Access** to windows jobs:
  - `windows-job-1`
  - `windows-job-2`
- **Additional Access**:
  - `windows-user-1` can view data jobs (`data-job-1` and `data-job-2`).

---

## **Summary**
The configuration ensures that:
1. **Data Engineers**:
   - Can build, configure, and view data jobs.
   - `data-engineer-1` can also view windows jobs.

2. **Windows Users**:
   - Can build, configure, and view windows jobs.
   - `windows-user-1` can also view data jobs.

This role-based setup ensures clear separation of responsibilities while allowing cross-role visibility where required.

---
