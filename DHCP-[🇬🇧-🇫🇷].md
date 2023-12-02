# Installation of ISC DHCP Server Package [🇬🇧]

## Installation:
```
sudo apt-get install isc-dhcp-server
```
Installs the ISC DHCP server on our system. A DHCP server automatically assigns IP addresses to devices on a network.

## Stopping the DHCP Service:
```
sudo systemctl stop isc-dhcp-server
```
Purpose: Stops the DHCP service. Useful for making configuration changes.

## Backup of DHCP Configuration File:
```
sudo cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.bak
```
Purpose: Creates a backup copy of the DHCP configuration file to prevent the loss of the current configuration.

## Changing the Network Interface for DHCP:
Edit: 
```/etc/default/isc-dhcp-server```
>[!note]
>Modify **INTERFACESv4** to specify our interface.

Example:
```
INTERFACESv4="ens33"
```
Purpose: Defines the network interface that the DHCP server will use to distribute IP addresses.

## DHCP Configuration:
Edit: ```/etc/dhcp/dhcpd.conf```
Add the following configuration:
```
subnet 192.168.81.0 netmask 255.255.255.0 {
   authoritative;
   range 192.168.81.13 192.168.81.50;
   option domain-name-servers 192.168.81.2;
   option domain-name "example.com";
   option routers 192.168.81.2;
   default-lease-time 3600;
   max-lease-time 3600;
   option subnet-mask 255.255.255.0;
   option broadcast-address 192.168.81.255;
}
```
Purpose: Defines the range of IP addresses to be distributed, DNS server, domain name, default router, and other options.

Restarting the DHCP Service:
```
sudo systemctl start isc-dhcp-server
```
-Restarts the DHCP service to apply the new configurations.

## Checking DHCP Leases:
```
less /var/lib/dhcp/dhcpd.leases
```
Displays the IP addresses currently assigned by the DHCP server.





# Installation du package ISC DHCP Server [🇫🇷]
## Installation:
```
sudo apt-get install isc-dhcp-server
```
Installe le serveur DHCP ISC sur notre système. Un serveur DHCP assigne automatiquement des adresses IP aux dispositifs dans un réseau.

## Arrêt du service DHCP
```
sudo systemctl stop isc-dhcp-server
```
Arrête le service DHCP. Utile pour effectuer des modifications de configuration.

## Sauvegarde du fichier de configuration DHCP
```
sudo cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.bak
```
Crée une copie de sauvegarde du fichier de configuration DHCP pour éviter la perte de la configuration actuelle.

## Modification de l'interface réseau pour DHCP
Éditer: ```/etc/default/isc-dhcp-server```
>[!note]
>Modifier **INTERFACESv4** pour y mettre votre interface

Example: 
```
INTERFACESv4="ens33"
```
Définir l'interface réseau que le serveur DHCP utilisera pour distribuer les adresses IP.

## Configuration du DHCP
Éditer: ```/etc/dhcp/dhcpd.conf```
Ajouter la configuration suivante:
```
subnet 192.168.81.0 netmask 255.255.255.0 {
   authoritative;
   range 192.168.81.13 192.168.81.50;
   option domain-name-servers 192.168.81.2;
   option domain-name "example.com";
   option routers 192.168.81.2;
   default-lease-time 3600;
   max-lease-time 3600;
   option subnet-mask 255.255.255.0;
   option broadcast-address 192.168.81.255;
}
```
Définir la plage d'adresses IP à distribuer, le serveur DNS, le nom de domaine, le routeur par défaut et d'autres options.

## Redémarrage du service DHCP
```
sudo systemctl start isc-dhcp-server
```
Redémarre le service DHCP pour appliquer les nouvelles configurations.

## Vérification des baux DHCP
```
less /var/lib/dhcp/dhcpd.leases
```
Affiche les adresses IP actuellement attribuées par le serveur DHCP.
