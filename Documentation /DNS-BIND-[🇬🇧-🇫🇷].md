# DNS Configuration with BIND [🇬🇧]

## BIND Installation
```
sudo apt-get install bind9 bind9utils bind9-doc
```
BIND is a DNS server that resolves domain names to IP addresses.

### Definition of DNS Zone
Configure DNS zones for domain name resolution and reverse DNS.
Edit: ```/etc/bind/named.conf.local```
Add:
```
zone "example.com" {
    type master;
    file "/etc/bind/db.example.com";
};

zone "153.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.153.168.192.in-addr.arpa";
};
```
## Configuration of Zone Files
Edit: ```/etc/bind/db.example.com```
Configure:
```
$TTL    604800
@       IN      SOA     ns.example.com. admin.example.com. (
    2023021509  ; Serial
    604800      ; Refresh
    86400       ; Retry
    2417200     ; Expire
    604800      ; Negative Cache TTL
)

@               IN      NS      ns.example.com.
ns              IN      A       192.168.81.2
netservice      IN      CNAME   ns

example.com.    IN  A   192.168.81.11

; Server
glpi       IN      A       192.168.81.11
www        IN      A       192.168.81.11
nas        IN      A       192.168.81.12
```
For reverse DNS,
Edit: ```/etc/bind/db.81.168.192.in-addr.arpa```
Configure:
```
$TTL 1800
@       IN      SOA     ns.example.com. admin.example.com. (3 14400 3600 604800 10800)

@       IN      NS      ns.
2       IN      PTR     ns.example.com.

11      IN      PTR     debiansrv.example.com.
12      IN      PTR     trisquel.example.com.

;150     IN      PTR     llithyie.example.com.
;151     IN      PTR     cerbere.example.com.
;152     IN      PTR     hermes.example.com.
;152     IN      PTR     smtp.example.com.
;152     IN      PTR     pop3.example.com.
;152     IN      PTR     mail.example.com.
```
## Modification of BIND Options:
Edit: ```/etc/bind/named.conf.options```
Configure:
```
options {
        directory "/var/cache/bind";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses
        // replacing the all-0's placeholder.

        //allow-recursion { localhost; };
        allow-recursion { any; };

        forwarders { 8.8.8.8; };

        dnssec-validation yes;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
        listen-on { any; };
        allow-query { any; };
};
```
Configure additional options for BIND, such as transfer servers, DNS security, etc.

Verification and Starting BIND
```
$ sudo named-checkconf
$ sudo named-checkzone example.com /etc/bind/db.example.com
zone example.com/IN: loaded serial 17032304
OK
$ sudo named-checkzone example.com /etc/bind/db.81.168.192.in-addr.arpa
zone example.com/IN: loaded serial 3
OK
```
Verify the BIND configuration and start the service.

Check configuration: 
```
sudo named-checkconf
```
Check zones: 
```
sudo named-checkzone example.com /etc/bind/db.example.com
```
Start BIND: 
```
sudo systemctl start bind9
```
Check status: 
```
sudo systemctl status bind9
```
