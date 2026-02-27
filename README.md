# Oracle 21c XE + SQL Developer on Oracle Linux 8

A complete step-by-step guide to set up a local Oracle Database 21c Express Edition (XE) with SQL Developer on Oracle Linux 8. Perfect for **1Z0-071 exam preparation** and daily SQL practice.

## 🧪 Tested on real hardware

This guide is not just theory — every step was verified on:

| Machine | Configuration | Status |
|---------|--------------|--------|
| ASUS UX303LN | i7-4510U, 8GB RAM, OL8.10 | ✅ Working |
| ThinkPad X240 | i7-4600U, 8GB RAM, OL8.10 | ✅ Working |
| HP EliteBook 840 G3 | i5-6300U, 16GB RAM, OL8.10 | ✅ Working |
| VM on iMac'2012 | 4 cores, 8GB RAM, OL8.10 guest | ✅ Working |
| VM on Mac mini'2012 | 4 cores, 8GB RAM, OL8.10 guest | ✅ Working |

[📊 Full neofetch logs](test-env/neofetch-logs.md)

## 📚 What's inside

- [Full guide in Russian](guide-ru.md) — detailed instructions with comments
- [Example config files](configs/) — ready-to-use .bash_profile, listener.ora
- [Troubleshooting](troubleshooting/) — common errors and fixes (ORA-12541, etc.)

## 🚀 Quick start

```bash
# 1. Update system
$ sudo dnf update -y

# 2. Install preinstall packages
$ sudo dnf install oracle-database-preinstall-21c -y

# 3. Download and install Oracle XE
$ wget https://download.oracle.com/otn-pub/otn_software/db-express/oracle-database-xe-21c-1.0-1.ol8.x86_64.rpm
$ sudo dnf install oracle-database-xe-21c-1.0-1.ol8.x86_64.rpm -y
```
>⚠️ Important: The next step is interactive. You must be at the terminal to type the password when prompted.
```bash
# 4. Configure database — you will be asked to set passwords for SYS, SYSTEM and PDBADMIN
$ sudo /etc/init.d/oracle-xe-21c configure
```
For complete step-by-step instructions with all details, see the [full guide](guide-ru.md).
