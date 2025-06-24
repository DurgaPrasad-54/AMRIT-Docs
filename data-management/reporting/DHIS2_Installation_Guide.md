# DHIS2 Installation Guide

## Overview

DHIS2 is an open-source platform used for health information systems. This guide will walk you through installing DHIS2 version v40.3.0 on a server running Ubuntu 22.04.

## Prerequisites

- A server running Ubuntu 22.04
- SSH access to the server with a non-root user having sudo privileges
- At least 8 GB RAM and 2 CPUs (recommended for production)

---

## Installation

### 1. Create a DHIS2 System User

DHIS2 on Tomcat should never be run as the root user. Instead, create a standard user and use it to run the Tomcat instance to enhance security.

**Steps:**

1. Create a user:
```
sudo useradd -d /home/dhis -m dhis -s /bin/false
```

3. Set the password for the created user:
```
sudo passwd dhis
```
Use a strong password with at least 15 random characters.

---

### 2. Set Up DHIS2 Configuration Directory

Create a config directory for the DHIS2 instance. This directory will also be used for apps, files, and log files.

**Steps:**

1. Create directory:
```
sudo -u dhis mkdir /home/dhis/config
```

---

### 3. Setting Server Timezone and Locale

Set your system timezone (recommended for logging and DB timestamps).

**Steps:**

1. Set timezone (Asia/Kolkata):
```
sudo dpkg-reconfigure tzdata
```
Choose **Asia → Kolkata → OK**.

---

### 4. PostgreSQL Installation

Install PostgreSQL with the following steps.

**Steps:**

1. Add PostgreSQL repository:
```
sudo sh -c 'echo "deb https://apt.postgresql.org/pub/repos/apt \$(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
```
2. Import the PostgreSQL repository signing key:
```
curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
```
3. Update the package lists:
```
sudo apt update -y \&\& sudo apt upgrade -y
```
4. Install PostgreSQL 16:
```
sudo apt-get install -y postgresql-16 postgresql-16-postgis-3
```
5. Ensure PostgreSQL is started and enabled:
```
sudo systemctl start postgresql
sudo systemctl enable postgresql
```
6. Create a non-privileged database user `dhis`:
```
sudo -u postgres createuser -SDRP dhis
```
Enter a secure password at the prompt.

7. Create a database named `dhis` owned by `dhis` database user:
```
sudo -u postgres createdb -O dhis dhis
```
8. Create extensions as postgres user:
```
sudo -u postgres psql -c "create extension postgis;" dhis
sudo -u postgres psql -c "create extension btree_gin;" dhis
sudo -u postgres psql -c "create extension pg_trgm;" dhis
```

---

### 5. Java Installation

The recommended Java JDK for DHIS2 2.40 and above is OpenJDK 17.

**Steps:**

1. Install openjdk-17:
```
sudo apt-get install -y openjdk-17-jdk
```
2. Check version of java:
```

java -version

```

---

### 6. DHIS2 Configuration

The database connection information is provided to DHIS2 through a configuration file, `dhis.conf`.

**Steps:**

1. Create and edit the file:
```
sudo -u dhis touch /home/dhis/config/dhis.conf
sudo -u dhis vim /home/dhis/config/dhis.conf
```
2. Paste the following (replace `xxxx` with your password):
```


# Database connection

connection.driver_class = org.postgresql.Driver
connection.url = jdbc:postgresql:dhis
connection.username = dhis
connection.password = xxxx

```
To save and exit: press `ESC`, then `:wq`.

---

### 7. Tomcat and DHIS2 Installation

**Steps:**

1. Install Tomcat user instance:
```
sudo apt-get install -y tomcat9-user
```
2. Create an instance named `tomcatdhis`:
```
sudo tomcat9-instance-create /home/dhis/tomcat-dhis
sudo chown -R dhis:dhis /home/dhis/tomcat-dhis/
```
3. Edit `setenv.sh` and append:
```
sudo -u dhis vim /home/dhis/tomcat-dhis/bin/setenv.sh
```
Paste:
```
export JAVA_HOME='/usr/lib/jvm/java-17-openjdk-amd64/'
export JAVA_OPTS='-Xms3g -Xmx6g'
export DHIS2_HOME='/home/dhis/config'
```
To save and exit: press `ESC`, then `:wq`.
4. Edit `startup.sh`:
```
sudo -u dhis vim /home/dhis/tomcat-dhis/bin/startup.sh
```
Paste:
```

\#!/bin/sh
set -e
if [ "\$(id -u)" -eq "0" ]; then
echo "This script must NOT be run as root" 1>\&2
exit 1
fi
export CATALINA_BASE="/home/dhis/tomcat-dhis"
/usr/share/tomcat9/bin/startup.sh
echo "Tomcat started"

```
To save and exit: press `ESC`, then `:wq`.

---

### 8. DHIS2 WAR File Deployment

**Steps:**

1. Download war file:
```
wget -O dhis.war https://releases.dhis2.org/40/dhis2-stable-40.3.0.war
```
2. Move the WAR file into the Tomcat webapps directory:
```
sudo mv dhis.war /home/dhis/tomcat-dhis/webapps/ROOT.war
```

---

### 9. Running DHIS2

**Steps:**

1. Start the DHIS2 instance:
```
sudo -u dhis /home/dhis/tomcat-dhis/bin/startup.sh
```

---

### 10. Check the Logs

**Steps:**

1. Check log:
```
tail -f /home/dhis/tomcat-dhis/logs/catalina.out
```

---

### 11. Stop DHIS2

**Steps:**

1. To stop the DHIS2 instance:
```
sudo -u dhis /home/dhis/tomcat-dhis/bin/shutdown.sh
```

---

## Access DHIS2

**URL:** https://your-server-ip:8080

**Default Login Credentials:**

- **Username:** admin
- **Password:** district

