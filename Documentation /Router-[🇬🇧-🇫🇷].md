# Router Installation and Configuration [🇬🇧]

## Adding a Second Network Card
Configure locally with DHCP disabled.
>[!note]
>Provides an additional network interface for routing.

## Network Interface Configuration
Edit: ```/etc/network/interfaces```
Configure according to your environment, for example:
```
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto ens33
allow-hotplug ens33
iface ens33 inet dhcp

# Router
auto ens36
iface ens36 inet static
address 192.168.81.2
dns-nameservers 192.168.81.2
network 192.168.81.0 
netmask 255.255.255.0 
broadcast 192.168.81.255
```
Configures the network interface, assigns a static IP address to the router's network interface, and provides its configuration.

## Restarting Network Services
```
sudo systemctl restart networking
```
Activate the specific interface: sudo ifup ens36

## Enabling IP Routing
Edit: ```/etc/sysctl.conf```
Uncomment:
```
net.ipv4.ip_forward=1 
net.ipv6.conf.all.forwarding=1
```
Apply changes: 
```
sudo sysctl -p
```
Check if the changes are applied by running: 
```
cat /proc/sys/net/ipv4/ip_forward
```
Activates IP routing on the server, allowing the transmission of packets between different network interfaces.

## Installation and Configuration of IPTABLES
```
sudo apt-get install iptables
```
Configure NAT: 
```
sudo iptables -t nat -A POSTROUTING -o ens33 -j MASQUERADE
```
Install IPTABLES Persistent: 
```
sudo apt-get install iptables-persistent
```
Save rules: 
```
sudo dpkg-reconfigure iptables-persistent
```
Check if rules are correctly defined: 
```
sudo iptables -L -t nat -v
```
IPTABLES is used to configure firewall rules. The configuration command establishes NAT for packet routing.
