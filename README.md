# Oracle 21c XE + SQL Developer on Oracle Linux 8

A complete step-by-step guide to set up a local Oracle Database 21c Express Edition (XE) with SQL Developer on Oracle Linux 8. Perfect for **1Z0-071 exam preparation** and daily SQL practice.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Oracle Linux](https://img.shields.io/badge/OS-Oracle%20Linux%208-red)](https://oracle.com/linux)
[![Oracle DB](https://img.shields.io/badge/Oracle-21c%20XE-blue)](https://oracle.com/database)

## 🧪 Tested on real hardware

This guide is not just theory — every step was verified on real machines:

| Machine | Configuration | Status |
|---------|--------------|--------|
| ASUS UX303LN | i7-4510U, 8GB RAM, OL8.10 | ✅ Working |
| ThinkPad X240 | i7-4600U, 8GB RAM, OL8.10 | ✅ Working |
| HP EliteBook 840 G3 | i5-6300U, 16GB RAM, OL8.10 | ✅ Working |
| VM on iMac'2012 | 4 cores, 8GB RAM, OL8.10 guest | ✅ Working |
| VM on Mac mini'2012 | 4 cores, 8GB RAM, OL8.10 guest | ✅ Working |

[📊 Full neofetch logs](test-env/neofetch-logs.md)

## 📚 What's inside

- **Step-by-step guides** in three languages:
  - [🇬🇧 English](guide-en.md)
  - [🇷🇺 Russian](guide-ru.md) 
  - [🇪🇸 Spanish](guide-es.md)
- [Example config files](configs/) — ready-to-use .bash_profile, listener.ora
- [Troubleshooting](troubleshooting/) — common errors and fixes (ORA-12541, etc.)

## 🎯 Why this guide?

- ✅ **Local setup** — no cloud, no remote servers, everything runs on your machine
- ✅ **Exam-focused** — includes all required privileges for 1Z0-071 practice
- ✅ **Family-proof** — Oracle Linux works as daily driver
- ✅ **Battle-tested** — verified on 5 different machines

## 🐛 Found an issue?

Feel free to [open an issue](../../issues) or submit a pull request. Let's make this guide better together!

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

