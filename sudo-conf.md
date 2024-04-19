# Configure SUDO on Linux
> [!note]
> Once you installed your Linux distribution, you're going to want to install sudo.
## Procedure
> [!important]
> To execute the following commands put your self in root bye typing the following command:
> ```su```

Enter your **password**.

Install **sudo**
```
apt install sudo
```
Enter the sudoers config file:
```
sudo visudo
```
Find the following line in the document:
> [!note]
> Allow members of group sudo to execute any command
>
> ```%sudo   ALL=(ALL:ALL) ALL```

Under ```%sudo   ALL=(ALL:ALL) ALL``` type the following line:

```
"user" ALL=(ALL) NOPASSWD:ALL
```

>[!important]
>Replace **"user"** with the Linux user you want to give sudo access to.
