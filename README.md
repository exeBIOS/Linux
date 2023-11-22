# Linux-FTP-Server
Here is the procedure i follow to configure a linux ftp server on Debian-11
##FTP server configuration

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

Change the following options (check with the search functions if they are already present using ```ctrl + w```):

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
## user chroot
Users will only be able to access a single directory from their FTP client. This base directory for vsftpd will be ```/srv/ftp/allusers```.

It is important to create this directory on the server.

To configure this directory, it will be necessary to use the following two directives in ```/etc/vsftpd.conf```:
