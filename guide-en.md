


# Setting Up Oracle SQL Learning Environment for 1Z0-071 Exam Preparation

Complete guide to install Oracle Database 21c XE, SQL Developer and configure a learning environment on Oracle Linux 8

## 📌 Who this guide is for

- Students preparing for 1Z0-071 exam (Oracle Database SQL)
- Developers needing a local Oracle DB for practice
- Those who want to use Oracle Linux as a daily driver

## 📊 Full specifications: [all neofetch logs](test-env/neofetch-logs.md) — for those who want to see detailed configuration of each machine.

> 💡 **Important**: All setup is done locally, no "remote servers" — just your own hardware.

## 🔧 System Requirements

- **OS**: Oracle Linux 8 (tested on version 8.10)
- **RAM**: minimum 4 GB (8 GB recommended for comfortable work)
- **Disk**: minimum 16 GB free space
- **Java**: OpenJDK 1.8 or higher (will be installed later)

> ⚠️ **Important**: Instructions tested only on Oracle Linux 8. Not tested on other distributions (RHEL, AlmaLinux, Rocky Linux) — configuration and packages may differ.

## 📦 System Preparation

### 1. Check hostname (important for listener)

```bash
# Check current name
$ hostname

# If name differs from localhost — edit hosts
$ sudo nano /etc/hosts
```

Add or uncomment:

```
127.0.0.1   localhost localhost.localdomain
127.0.0.1   oracle8  oracle8.me   # replace with your hostname
```

### 2. Update system and install basic packages

```bash
$ cd ~
$ sudo dnf update -y
$ sudo dnf install wget nano -y
```

## 🗄️ Installing Oracle Database 21c XE

### 3. Install preinstall dependencies

```bash
$ sudo dnf install oracle-database-preinstall-21c -y
```

### 4. Download and install database

```bash
# Download RPM package
$ wget https://download.oracle.com/otn-pub/otn_software/db-express/oracle-database-xe-21c-1.0-1.ol8.x86_64.rpm

# Install
$ sudo dnf install oracle-database-xe-21c-1.0-1.ol8.x86_64.rpm -y
```

### 5. Start service and enable autostart

```bash
$ sudo systemctl enable oracle-xe-21c
$ sudo systemctl start oracle-xe-21c
$ sudo systemctl status oracle-xe-21c   # check if everything is OK
```

## 🔑 Initial Database Setup

### 6. Configure environment variables for user

```bash
$ nano ~/.bash_profile
```

Add to the end of file:

```bash
export ORACLE_HOME=/opt/oracle/product/21c/dbhomeXE
export ORACLE_SID=XE
export PATH=$ORACLE_HOME/bin:$PATH
```

Apply changes:

```bash
$ source ~/.bash_profile
```

### 7. Configure database (set password)

```bash
$ sudo /etc/init.d/oracle-xe-21c configure
```

During setup you'll be prompted to set password for system users (SYS, SYSTEM, PDBADMIN). **Remember it!**

### 8. Add user to dba group

```bash
$ sudo usermod -aG dba $USER
# Log out and log back in to apply group changes
# or run:
$ newgrp dba
```

### 9. Test database connection

```bash
$ sqlplus / as sysdba
```

## 👤 Create Learning User

### 10. Switch to correct container (PDB)

```sql
SQL> SHOW CON_NAME;   -- should be CDB$ROOT
SQL> ALTER SESSION SET CONTAINER = XEPDB1;
SQL> SHOW CON_NAME;   -- now XEPDB1
```

### 11. Create student user

```sql
SQL>CREATE USER student IDENTIFIED BY student
  DEFAULT TABLESPACE users
  TEMPORARY TABLESPACE temp
  QUOTA UNLIMITED ON users;
```

### 12. Grant necessary privileges

```sql
-- Basic connection and work privileges
SQL>GRANT CONNECT, RESOURCE TO student;
-- or more modern approach:
SQL>GRANT CREATE SESSION TO student;

-- Object creation privileges
SQL>GRANT CREATE VIEW, CREATE SEQUENCE, CREATE PROCEDURE TO student;
SQL>GRANT CREATE TRIGGER TO student;  -- if needed

-- Exit
SQL>EXIT
```

### 13. Test connection as student

```bash
$ sqlplus student/student@localhost:1521/XEPDB1
```

### 14. Create test table

```sql
SQL>CREATE TABLE test_table (
  id NUMBER PRIMARY KEY,
  name VARCHAR2(50),
  created_at DATE DEFAULT SYSDATE
);

SQL>INSERT INTO test_table (id, name) VALUES (1, 'Ivan');
SQL>INSERT INTO test_table (id, name) VALUES (2, 'Maria');
SQL>INSERT INTO test_table (id, name) VALUES (3, 'Antonio');

SQL>COMMIT;

SQL>SELECT * FROM test_table;

SQL>EXIT;
```

## ☕ Installing Java and SQL Developer

### 15. Install Java 17 (recommended for latest SQL Developer)

```bash
$ sudo dnf install java-17-openjdk java-17-openjdk-devel -y

# Check default version
$ sudo alternatives --config java   # select 17 if needed

# Set JAVA_HOME
$ echo 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk' >> ~/.bashrc
$ echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc
$ source ~/.bashrc

# Verify
$ java --version
```

### 16. Download and install SQL Developer

```bash
$ wget https://download.oracle.com/otn_software/java/sqldeveloper/sqldeveloper-24.3.1-347.1826.noarch.rpm
$ sudo dnf install sqldeveloper-24.3.1-*.rpm -y
```

### 17. Launch (test)

```bash
$ sqldeveloper&
```

## 🔌 Configuring Connection in SQL Developer

Click green "+" (New Connection)
Fill in the fields:

| Field | Value |
|-------|-------|
| **Connection Name** | XE Student (any name) |
| **Username** | student |
| **Password** | student |
| **Save Password** | ✓ (optional) |
| **Connection Type** | Basic |
| **Hostname** | localhost (or your machine's IP) |
| **Port** | 1521 |
| **Service Name** | XEPDB1 |

![New Connection](images/new-connect.png)

Click **Test** — should show "Success"
Click **Save**, then **Connect**

### Verification

In the opened SQL Developer window execute:

```sql
SELECT * FROM test_table;
```

Click green arrow (F5). You should see:

```
ID | NAME    | CREATED_AT
---|---------|------------------
1  | Ivan    | 27-FEB-26
2  | Maria   | 27-FEB-26
3  | Antonio | 27-FEB-26
```

![Test Results](images/testing-oracle-sql-developer.png)

## 🐛 Common Errors and Solutions

### ❌ ORA-12541: TNS:no listener

**Symptom**: Getting error when connecting with `sqlplus student/student@localhost:1521/XEPDB1`

**Solution**:

1. Switch to oracle user:
```bash
$ sudo su - oracle
```

2. Check listener configuration:
```bash
# Add environment variables for oracle user (if not already added)
$ echo "export ORACLE_HOME=/opt/oracle/product/21c/dbhomeXE" >> ~/.bash_profile
$ echo "export ORACLE_SID=XE" >> ~/.bash_profile
$ echo "export PATH=$ORACLE_HOME/bin:$PATH" >> ~/.bash_profile
$ source ~/.bash_profile

# Check status
$ lsnrctl status
```

3. **Important**: Pay attention to PORT in output! In my case it was 1539, not 1521:
```
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=ol8)(PORT=1539)))
```

4. Check listener.ora config:
```bash
$ cat $ORACLE_HOME/network/admin/listener.ora
```

5. If port differs, connect using it:
```bash
$ sqlplus student/student@localhost:1539/XEPDB1   # instead of 1521
```

6. Or restart listener:
```bash
$ lsnrctl stop
$ lsnrctl start
# or
$ lsnrctl reload
```

> 💡 **Tip**: If you change port in listener.ora, don't forget to update connections in SQL Developer and restart listener!

## ✅ What's Next?

Now you have a fully working environment for:

- 1Z0-071 exam preparation
- Learning Oracle SQL
- Developing applications with Oracle DB

Everything works locally, without internet, on your hardware. Your family can continue using the computer for daily tasks — Oracle Linux works great as a workstation.

## 📚 Useful Links

- [Official Oracle Database 21c XE Documentation](https://docs.oracle.com/en/database/oracle/oracle-database/21/xeinl/)
- [1Z0-071 Exam Specification](https://education.oracle.com/oracle-database-sql/pexam_1Z0-071)
- [SQL Developer Documentation](https://docs.oracle.com/en/database/oracle/sql-developer/)

---

*Found an error? Have questions? Open an issue in the repository or start a discussion!*
```

