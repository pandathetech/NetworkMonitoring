# Zabbix

---

## Table of Contents
- [1. Choose Your Platform](#1-choose-your-platform)
- [2. Install Zabbix Repository](#2-install-zabbix-repository)
- [3. Install Zabbix Components](#3-install-zabbix-components)
- [4. Install SQL](#4-install-sql)
- [5. Create a Database in SQL](#5-create-a-database-in-sql)
- [6. Configure the Database for Zabbix](#6-configure-the-database-for-zabbix)
- [7. Enable and Start Zabbix](#7-enable-and-start-zabbix)
- [8. Zabbix's User Interface Page](#8-zabbixs-user-interface-page)
- [9. References](#9-references)

---

## 1. Choose Your Platform
You can choose your platform to install Zabbix among the downloads in the official Zabbix website : [Zabbix Downloads](https://www.zabbix.com/download).

In my demonstration guide, I installed Zabbix 7.4 (the latest version at the time of this editing) on an Ubuntu 22.04 VM with MySQL and Apache as my database and web servers installed respectively.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20232231.png)

---

## 2. Install Zabbix Repository
Download the official Zabbix repository package for Ubuntu 22.04:

```
sudo wget https://repo.zabbix.com/zabbix/7.4/release/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.4+ubuntu22.04_all.deb
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20133657.png)

Install the downloaded Zabbix repository package.

```
sudo dpkg -i zabbix-release_latest_7.4+ubuntu22.04_all.deb
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20133657.png)

Update the system package list to include the newly added Zabbix repository.

```
sudo apt update
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20134103.png)

---

## 3. Install Zabbix Components
Install the Zabbix server, frontend, agent, and required dependencies using the following command:

```
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent
```

> Enter `Y` for Yes.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20134224.png)

---

## 4. Install SQL
I installed the MySQL server.

```
sudo apt install mysql-server
```

> Enter `Y` for Yes.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20140417.png)

---

## 5. Create a Database in SQL
Log in to the MySQL shell as the root user.

```
sudo mysql -uroot -p
```

> The login password to the MySQL shell is your system password.

Create the Zabbix database with UTF-8 support.

```
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
```

Create a dedicated MySQL user for Zabbix.

```
CREATE USER zabbix@localhost IDENTIFIED BY 'password';
```

> ⚠️ Replace the password variable with a password of your choice. This will be the same password used for your Zabbix server's database.

Grant full permissions on the Zabbix database to the new user.

```
GRANT ALL PRIVILEGES ON zabbix.* TO zabbix@localhost;
```

Temporarily enable function creation required for importing the Zabbix schema.

```
SET GLOBAL log_bin_trust_function_creators = 1;
```

Exit the MySQL shell.

```
QUIT;
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20141645.png)

Import the initial Zabbix database schema and data. You will be prompted to enter your newly created password from your SQL shell.

```
sudo zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20142059.png)

Log back into MySQL as root.

```
sudo mysql -uroot -p
```

Disable function creation once the import is complete.

```
SET GLOBAL log_bin_trust_function_creators = 0;
```

Exit the MySQL shell.

```
QUIT;
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20142256.png)

---

## 6. Configure the Database for Zabbix
Access the `/etc/zabbix/` directory and edit the `zabbix_server.conf` file.

```
cd /etc/zabbix/
```

```
sudo vim zabbix_server.conf
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20142340.png)

Uncomment the `DBHost` and `DBPassword` lines.

> For DBHost, you should set it to your Zabbix server IP. As for DBPassword, enter your newly created password in SQL earlier.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20142517.png)

---

## 7. Enable and Start Zabbix
Restart your Zabbix server and agent processes.

```
sudo systemctl restart zabbix-server zabbix-agent apache2
```

Enable your Zabbix server and agent processes at startup boot.

```
sudo systemctl enable zabbix-server zabbix-agent apache2
```

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20142626.png)

---

## 8. Zabbix's User Interface Page
You can access your Zabbix UI page by entering the following URL on a web browser.

```
http://<zabbix-server-ip>/zabbix
```

> If you installed Zabbix locally in an environment that has GUI (e.g. Ubuntu 22.04 Desktop), you could replace the <zabbix-server-ip> variable with `localhost`.

Once you are in the page, select your default language. Click `Next step`.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20142734.png)

Ensure that all your prerequisites are OK. Click `Next step`.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20142816.png)

Configure your database connection. The necessary fields to edit are `Database host` and `Password`.

> The database host is your Zabbix server IP (or localhost). Your Zabbix password was generated in SQL earlier.

Click `Next step`.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20142931.png)

Click `Next step`.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20143147.png)

Click `Next step`.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20143228.png)

Click `Finish`.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20143312.png)

After trying to sign in using the provided credentials, it gave me an error. The right credentials are `Admin` (username) and `zabbix` (password).

Click `Sign In`.

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20143407.png)

![](http://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20143526.png)

You are now officially connected to your Zabbix server home page !

![](https://github.com/pandathetech/ITMonitoring/blob/main/Zabbix/Assets/Screenshot%202026-01-07%20143607.png)

---

## 9. References
- [Zabbix - Installation on Ubuntu 22.04](https://www.zabbix.com/download?zabbix=7.4&os_distribution=ubuntu&os_version=22.04&components=server_frontend_agent&db=mysql&ws=apache)
