
# Oracle PDB Assignment II

**Student:** Januario  
**Student ID:** 27528  
**Course:** Database Development with PL/SQL (INSY 8311)  
**Instructor:** Eric Maniraguha  
**Date:** February 16, 2026  

---

## Overview of Tasks

This assignment demonstrates practical knowledge of Oracle Database multitenant architecture through four main tasks:

1. **Task 1:** Create a Pluggable Database (PDB) with a dedicated user for class work  
2. **Task 2:** Create and delete a temporary PDB  
3. **Task 3:** Configure and access Oracle Enterprise Manager (OEM)  
4. **Task 4:** Document the process and publish on GitHub  

---

## Oracle Environment Used

| Component | Details |
|-----------|---------|
| **Oracle Version** | Oracle Database 21c Enterprise Edition (21.3.0.0.0) |
| **CDB Name** | ORCL |
| **Operating System** | Microsoft Windows x86 64-bit |
| **Installation Path** | C:\ORACLE21C\ |
| **Datafiles Path** | C:\ORACLE21C\ORADATA\ORCL\ |
| **OEM Port** | HTTPS: 5500 |

---

## Task 1: Create a New Pluggable Database

### Objective
Create a PDB with a dedicated user account following the naming convention:
- **PDB Name:** `ja_pdb_27528`
- **Username:** `januario_plsqlauca_27528`
- **Password:** `auca`

### Steps Performed

```sql
-- 1. Connect as SYSDBA
sqlplus sys as sysdba

-- 2. Create the PDB
CREATE PLUGGABLE DATABASE ja_pdb_27528
ADMIN USER januario_plsqlauca_27528 IDENTIFIED BY auca
FILE_NAME_CONVERT = ('C:\ORACLE21C\ORADATA\ORCL\PDBSEED\',
                     'C:\ORACLE21C\ORADATA\ORCL\ja_pdb_27528\');

-- 3. Open the PDB
ALTER PLUGGABLE DATABASE ja_pdb_27528 OPEN;

-- 4. Switch to the PDB and grant privileges
ALTER SESSION SET CONTAINER = ja_pdb_27528;
GRANT CONNECT, RESOURCE, DBA TO januario_plsqlauca_27528;
GRANT UNLIMITED TABLESPACE TO januario_plsqlauca_27528;
GRANT CREATE SESSION, CREATE TABLE, CREATE VIEW,
      CREATE PROCEDURE, CREATE SEQUENCE, CREATE TRIGGER
      TO januario_plsqlauca_27528;
```

### Results
✅ PDB `ja_pdb_27528` created successfully  
✅ User `januario_plsqlauca_27528` created with OPEN status  
✅ PDB opened in READ WRITE mode  
✅ User ready for future class work

### Screenshots
![Task 1 - PDB Creation](screenshots/task1_pdb_creation.png)
*Figure 1.1: PDB creation command*

![Task 1 - User Created](screenshots/task1_user_created.png)
*Figure 1.2: User verification in PDB*

![Task 1 - PDB Open State](screenshots/task1_pdb_open.png)
*Figure 1.3: PDB opened in READ WRITE mode*

---

## Task 2: Create and Delete a Temporary PDB

### Objective
Create a temporary PDB following the naming convention and then completely remove it:
- **Temporary PDB Name:** `ja_to_delete_pdb_27528`

### Steps Performed

```sql
-- 1. Create temporary PDB
CREATE PLUGGABLE DATABASE ja_to_delete_pdb_27528
ADMIN USER temp_admin IDENTIFIED BY auca
FILE_NAME_CONVERT = ('C:\ORACLE21C\ORADATA\ORCL\PDBSEED\',
                     'C:\ORACLE21C\ORADATA\ORCL\ja_to_delete_pdb_27528\');

-- 2. Open the temporary PDB
ALTER PLUGGABLE DATABASE ja_to_delete_pdb_27528 OPEN;

-- 3. Verify creation
SELECT name, open_mode FROM v$pdbs;

-- 4. Close the PDB (required before deletion)
ALTER PLUGGABLE DATABASE ja_to_delete_pdb_27528 CLOSE IMMEDIATE;

-- 5. Drop the PDB completely
DROP PLUGGABLE DATABASE ja_to_delete_pdb_27528 INCLUDING DATAFILES;

-- 6. Verify deletion
SELECT name, open_mode FROM v$pdbs;
```

### Results
✅ Temporary PDB created and opened successfully  
✅ PDB closed with IMMEDIATE option  
✅ PDB dropped with INCLUDING DATAFILES  
✅ Verified deletion - PDB no longer appears in v$pdbs

### Screenshots
![Task 2 - Temporary PDB Creation](screenshots/task2_pdb_creation.png)
*Figure 2.1: Temporary PDB created and opened*

![Task 2 - PDB Deletion](screenshots/task2_pdb_deletion.png)
*Figure 2.2: PDB successfully deleted*

---

## Task 3: Oracle Enterprise Manager (OEM)

### Objective
Configure and access Oracle Enterprise Manager to monitor the database environment.

### Steps Performed

```sql
-- 1. Connect as SYSDBA
sqlplus sys as sysdba

-- 2. Check current ports
SELECT DBMS_XDB_CONFIG.GETHTTPSPORT() FROM dual;  -- Result: 5500
SELECT DBMS_XDB_CONFIG.GETHTTPPORT() FROM dual;   -- Result: 0

-- 3. HTTPS port 5500 was already configured
-- 4. Access OEM via browser
```

### Access Information
- **URL:** `https://localhost:5500/em`
- **Login Credentials:**
  - Username: `januario_plsqlauca_27528`
  - Password: `auca`
  - Container: `JA_PDB_27528`

### Results
✅ OEM successfully accessed  
✅ Dashboard shows Oracle environment (ORCL 21c)  
✅ PDB `ja_pdb_27528` visible in the environment  
✅ Username visible in the session

### Screenshot
![Task 3 - OEM Dashboard](screenshots/task3_oem_dashboard.png)
*Figure 3.1: Oracle Enterprise Manager showing database status and PDB information*

---

## Challenges Faced and Solutions

| Challenge | Solution |
|-----------|----------|
| **Finding correct datafile paths** | Used `SELECT name FROM v$datafile;` to identify actual Oracle installation path (C:\ORACLE21C\ORADATA\ORCL\) |
| **PDB initially in MOUNTED state** | Used `ALTER PLUGGABLE DATABASE ... OPEN;` command to change to READ WRITE mode |
| **ORA-65096: invalid common user error** | Understood that common users in CDB$ROOT need C## prefix. Created users inside the PDB instead |
| **ORA-01012: not logged on** | Database was in idle state. Used `STARTUP` command to start the database instance |
| **OEM login issues** | Created a dedicated user `oem_admin` in the PDB with necessary privileges |
| **EXER command error** | Corrected to `EXEC` for executing PL/SQL procedures |

---

## Integrity Statement

I, Januario (Student ID: 27528), declare that this work is my own and has been completed in accordance with the course academic integrity policy. All screenshots are original and unaltered, documenting the actual execution of the required tasks.

The work presented in this repository represents my genuine effort to complete Assignment II - Oracle Database Environment (CDBs & PDBs) & OEM.

---

## Repository Structure

```
oracle_pdb_ass_II_27528_januario/
├── README.md
└── screenshots/
    ├── task1_pdb_creation.png
    ├── task1_user_created.png
    ├── task1_pdb_open.png
    ├── task2_pdb_creation.png
    ├── task2_pdb_deletion.png
    └── task3_oem_dashboard.png
```

---

**GitHub Repository:** [https://github.com/januario/oracle_pdb_ass_II_27528_januario](https://github.com/januario/oracle_pdb_ass_II_27528_januario)

**Submission Date:** February 16, 2026

@Januario_Henrique
https://www.linkedin.com/in/januario-henrique-98335a314/



