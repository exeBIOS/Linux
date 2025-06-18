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
