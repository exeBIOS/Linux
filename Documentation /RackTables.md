# RackTables
## Installation process on Fedora
### Install Dependencies
```
sudo dnf install httpd php php-mysqli php-mbstring php-gd php-pdo php-json mariadb-server unzip git
```
### Start Apache and MariaDB
```
sudo systemctl enable --now httpd mariadb
```
### Clone RackTables from Github
```
cd /var/www/html
sudo git clone https://github.com/RackTables/racktables.git
sudo mv racktables/wwwroot racktables
sudo rm -r racktables/.git
```
### Set Permissions
```
sudo chown -R apache:apache /var/www/html/racktables
sudo chmod -R 755 /var/www/html/racktables
```
### Create Database
```
sudo mysql_secure_installation
sudo mysql -u root -p
```

Inside MySQL type the following commands:
>[!caution]
> Don't Forget to change the user **Name** and **Password** in the MySQL code !!
```
CREATE DATABASE racktables DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'dasmin'@'localhost' IDENTIFIED BY 'Azerty.123';
GRANT ALL PRIVILEGES ON racktables.* TO 'dasmin'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### Configure Firewall
```
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --add-service=https --permanent
sudo firewall-cmd --reload
```

### Access the Web installer
```
http://192.168.1.56/racktables
```
## Get access to the web configuration interface
```
sudo mv /var/www/html/racktables/wwwroot /var/www/html/racktables-web
```
### Give permissions
```
sudo chown -R apache:apache /var/www/html/racktables-web
sudo chmod -R 755 /var/www/html/racktables-web
```
### Install php-bcmath packages
```
sudo dnf install php-bcmath
```
### Create the secret.php file :

```
sudo touch /var/www/html/racktables-web/inc/secret.php
```
### Give permissions

```
sudo chmod 666 /var/www/html/racktables-web/inc/secret.php
```
### Change Apache's User account name

```
sudo chown apache:apache /var/www/html/racktables-web/inc/secret.php
```
(Temporarly) deactivate SELinux to continue the installation :

```
sudo setenforce 0
```
You can reactivate SElinux with this command :

```
sudo setenforce 1
```
✅ Make the file accessible to apache

```
sudo chown -R apache:apache /var/www/html/racktables-web
sudo chmod -R 755 /var/www/html/racktables-web
```
### Start MariaDB
```
sudo systemctl start mariadb
```
### Connect to MySQL:
```
mysql -u root -p
```

### Create the database:
>[!caution]
> Don't Forget to change the user **Name** and **Password** in the MySQL code !!
>You might also get this error: *ERROR 1396 (HY000): Operation CREATE USER failed for 'user'@'localhost'*.
>Tghis is because the user you are entering already exists in Maria DB.
```
CREATE DATABASE racktables_db CHARACTER SET utf8 COLLATE utf8_general_ci;
CREATE USER 'racktables_user'@'localhost' IDENTIFIED BY 'tonmotdepasse';
GRANT ALL PRIVILEGES ON racktables_db.* TO 'racktables_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
### Enter the following values:
| Champ    | Valeur          |
| -------- | --------------- |
| database | `racktables_db` |
| username | `dasmin`        |
| password | `Azerty.123`    |

Click on **retry**.

### Change the permissions to continue
Give the properties to the Apache User
```
sudo chown apache:apache /var/www/html/racktables-web/inc/secret.php
```
Configure the correct permissions *Read Only*.
```
sudo chmod 400 /var/www/html/racktables-web/inc/secret.php
```
Verify the rights:
```
ls -l /var/www/html/racktables-web/inc/secret.php
```

