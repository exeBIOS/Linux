# Linux-FTP-Server: Installing VSFTP
Here is the procedure i followed to configure a linux ftp server on Debian-11
# FTP server configuration

## Introduction

VSFTP allows you to secure an FTP server using SSL or TLS protocols.

The objective of this part is to install a Debian server on which we will add an FTP service.

## Installation and configuration

### Installation
```
sudo apt-get update && sudo apt-get -y install vsftpd openssl
```

### Key generation
```
ll -d /etc/ssl/private
```

Check that the rights on this directory are 700. Otherwise, run the appropriate command for this.
And if the directory does not exist, create it and assign it rights 700.
We now create the key:

```
openssl req -x509 -nodes -days 365 -newkey rsa:3072 -keyout /etc/ssl/private/vsftpd_key.pem -out /etc/ssl/private/vsftpd.pem
```
```
Generating a 1024 bit RSA private key
....++++++
.....++++++
writing new private key to '/etc/ssl/private/vsftpd_key.pem'
-----
```
The following questions must now be answered:
```
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:USA
State or Province Name (full name) [Some-State]:California
Locality Name (eg, city) []:San Fransico
Organization Name (eg, company) [Internet Widgits Pty Ltd]:<TRAINING ORGANIZATION>
Organizational Unit Name (eg, section) []:<NAME OF TRAINING>
Common Name (e.g. server FQDN or YOUR name) []:example.com
Email Address []:<admin@example.com>
```
## Configuring VSFTP
The ```/etc/vsftpd.conf``` file allows you to modify various elements.

Change the following options (*check with the search functions if they are already present using* ```ctrl + w```):

- Activates SSL
```
ssl_enable=YES
```
- Allow anonymous users to use secured SSL connections
```
allow_anon_ssl=YES
```
- All non-anonymous logins are forced to use a secure SSL connection in order to send and receive data on data connections.
```
force_local_data_ssl=YES
```
- All non-anonymous logins are forced to use a secure SSL connection in order to send the password.
```
force_local_logins_ssl=YES
```
- Permit TLS v1 protocol connections. TLS v1 connections are preferred
```
ssl_tlsv1=YES
```
- Permit SSL v2 protocol connections. TLS v1 connections are preferred
```
ssl_sslv2=NO
```
- permit SSL v3 protocol connections. TLS v1 connections are preferred
```
ssl_sslv3=NO
```
- Disable SSL session reuse (required by WinSCP)
```
require_ssl_reuse=NO
```
- Select which SSL ciphers vsftpd will allow for encrypted SSL connections (required by FileZilla)
```
ssl_ciphers=HIGH
```
## Forcing TLS connections
Using (above) force_local_logins_ssl=YES and force_local_data_ssl=YES, then only TLS connections are secure.

This may block users with an old FTP client (which does not support TLS), but provides a better level of security.

Using force_local_logins_ssl=NO and force_local_data_ssl=NO TLS and non-TLS connections will be allowed, depending on the capabilities of the FTP clients (but this option is not recommended if you want to secure the server)
## Changing the certificate
Edit lines containing the following variables:
```
rsa_cert_file
rsa_private_key_file
```
And position the paths to the certificate files generated previously:
```
rsa_cert_file=/etc/ssl/private/vsftpd.pem
rsa_private_key_file=/etc/ssl/private/vsftpd_key.pem
```
## Connection with system users
The following options must be defined in order to allow system user connections:
```
# Uncomment this to allow local users to log in.
local_enable=YES
#
# Uncomment this to enable any form of FTP write command.
write_enable=YES
#
# Default umask for local users is 077. You may wish to change this to 02$# if your users expect that (022 is used by most other ftpd's)
local_umask=022
```
## User chroot
Users will only be able to access a single directory from their FTP client. This base directory for vsftpd will be ```/srv/ftp/allusers```.

It is important to create this directory on the server.

To configure this directory, it will be necessary to use the following two directives in ```/etc/vsftpd.conf```:
```
chroot_local_user=YES
local_root=/srv/ftp/allusers
```
## Restarting the FTP service
The restart is done with the following command:
```
sudo systemctl restart vsftpd
```
If no configuration errors are present, the service should start without errors. This can be checked with the following command:
```
sudo systemctl status vsftpd
```
## Testing with the Filezilla client
Download the FileZilla client and verify that the connection works using one of the users declared on the system.
**Using a declared user can be problematic from a security point of view: this means in particular users who are given usernames/passwords to connect will also be able to connect via SSH.
The next step suggests managing FTP users separately from system users.**
## User Management
## Creating a dedicated user
We will create a user and position their home directory in ```/var/ftproot```.

In addition, we will ensure that this user can only connect to this server to the FTP service (he will not be able to connect directly via **ssh**). This helps secure the server and prevents an FTP user (*who may be external to the organization*) from also being connected to the server as a standard user.
## Adding a disabled user
We will therefore create a user named **external**
We create the directory if it does not exist:
```
mkdir /var/ftproot
```
We add the user:
```
adduser --disabled-login --shell /bin/false --home /var/ftproot/externe externe
```
This last command will ask for details:
```
adduser --disabled-login --shell /bin/false --home /var/ftproot/externe externe
```
```
Adding user 'externe' ...
Adding new group 'externe' (1001) ...
Adding new user 'externe' (1001) with group 'externe' ...
Creating home directory '/var/ftproot/externe' ...
Copying files from '/etc/skel' ...
Changing the user information for externe
Enter the new value, or press ENTER for the default
    Full Name []:
    Room Number []:
    Work Phone []:
    Home Phone []:
    Other []:
Is the information correct? [Y/n]
```
We answer y (or press enter directly, knowing that the capital letter designates the default option).
To avoid having to answer all these questions we could also have written the following command:
```
sudo adduser --gecos ',,,,' --disabled-login --shell /bin/false --home /var/ftproot/externe externe
```
*gecos allows you to automatically fill in the `Full Name`, `Room Number`, etc. fields. Each field is separated by a comma.*
## Activation of the PAM service
The following line must be present in the vsftpd.conf file:
```
pam_service_name=vsftpd
```
### Connection via an independent user database
In this case, the external user will have a password not referenced in the directory and specific to the FTP server.
To configure this password, here is the procedure to follow below.
First thing to do: a backup of the file ```/etc/pam.d/vsftpd``` (to for example ```/etc/pam.d/vsftpd.bak```)
### **Configuring pam_userdb**
We will use the ```pam_userdb.so``` file
Then, replace the contents of ```/etc/pam.d/vsftpd``` with the following lines:
```
auth required pam_userdb.so db=/etc/vsftpd/login
account required pam_userdb.so db=/etc/vsftpd/login
session  required  pam_loginuid.so
```
It may be necessary to indicate the full path to pam_userdb.so (in the file ```/etc/pam.d/vsftpd```).
To do this, retrieve its full path using the following command:
```
find /lib -name pam_userdb.so
```
Command output: ```/lib/x86_64-linux-gnu/security/pam_userdb.so```
**Check that you are pointing to ```/etc/vsftpd/login``` (***the .db extension is added automatically by PAM, you should not put it otherwise file not found error***). In fact, the best is to create this file (***see next paragraph***)**
# PAM User Database
**In this section, we will enable the previously created user to be usable only in VSFTP.**
We will create a local password database:
```
sudo mkdir /etc/vsftpd/
```
```
sudo touch /etc/vsftpd/login.txt
```
```
sudo chmod 700 /etc/vsftpd
```
```
sudo chmod 600 /etc/vsftpd/login.txt
```
We edit the file ```/etc/vsftpd/login.txt``` using the following model:
```
user1
password1
user2
password2
```
Add a password for the **external** user.
It is necessary to install the *Berkeley Database Utilities* utility. To do this, we determine its version:
```
apt-cache search db | grep Berkeley | grep 'db.\+-util'
```
Command output:
```
db-upgrade-util - Berkeley Database Utilities (old versions)
db5.3-sql-util - Berkeley v5.3 SQL Database Utilities
db5.3-util - Berkeley v5.3 Database Utilities
```
This 5.3 version number allows us to know the packages to install (*replace them if the version number changes*):
```
sudo apt-get install libdb5.3 db5.3-util db5.3-doc
```
We can now generate the db:
```
sudo /usr/bin/db5.3_load -T -t hash -f /etc/vsftpd/login.txt /etc/vsftpd/login.db
```
*It is better to delete the ```/etc/vsftpd/login.txt``` file for added security.*
# Editing vsftpd.conf for guests
Add the following two lines to the configuration file:
```
guest_enable=YES
guest_username=ftp
```
**Restart the service**
# Configuration spécifique pour un utilisateur
In order to force a user to work in a particular folder, you must add a file for that user to a particular folder.
**Preparation**
Create the ```/etc/vsftpd/vsftpd_user_conf``` folder
Add the following line to ```/etc/vsftpd.conf```:
```
user_config_dir=/etc/vsftpd/vsftpd_user_conf
```
**Configuration**
For each user, you must create a file in this folder ```/etc/vsftpd/vsftpd_user_conf```.
For example, in the file ```/etc/vsftpd/vsftpd_user_conf/user1```, add and adapt the following elements:
```
## the user is locked in a specific folder
local_root=/path/to/folder/user1

## we do not want read only (Read only)
anon_world_readable_only=NO

## write permission (upload)
#write_enable=

anon_upload_enable=YES

## create folders
anon_mkdir_write_enable=YES

## right to rename, delete...
anon_other_write_enable=YES

## to manage user chmod
## enable option
virtual_use_local_privs=YES
## set local_umask option
local_umask=022
anon_umask=022
allow_writeable_chroot=YES

guest_enable=YES
guest_username=ftp
```
> The folder ```/path/to/folder/user1``` must be created and existing.
> Position the rights for the **ftp** user on this folder (*according to needs and rights*)

**Restart the service.**
## Connection with Filezilla
The next steps will be done on your client
## Creating a bookmark
Open session manager
The following window appears







