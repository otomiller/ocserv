# ocserv

## Install on Rocky, Tested on Rocky 9.1


Install Ocserv
```
dnf install ocserv
```

Check Status
```
systemctl start ocserv
systemctl status ocserv
```

Generate Certificate
```
mkdir /var/www/ocserv
chown www-data:www-data /var/www/ocserv -R
certbot certonly --webroot --agree-tos --email you@exmaple.com -d vpn.example.com -w /var/www/ocserv
```

Choose Authentication Method
```
PAM, Authentication File, RADIUS, etc
```

Important Parameters
```
tcp-port = 443
udp-port = 443

server-cert = /etc/ssl/certs/ssl-cert-snakeoil.pem
server-key = /etc/ssl/private/ssl-cert-snakeoil.key

max-clients = 128
max-same-clients = 2

keepalive = 30

try-mtu-discovery = false

idle-timeout=1200
mobile-idle-timeout=1800

default-domain = vpn.example.com

ipv4-network = 192.168.1.0
ipv4-netmask = 255.255.255.0

tunnel-all-dns = true

dns = 8.8.8.8
dns = 1.1.1.1

route = 10.0.0.0/8
or 
route = default

no-route = 192.168.5.0/255.255.255.0

cisco-client-compat = true
```

In case of Masuerading, maybe this commands will help you
```
echo "net.ipv4.ip_forward = 1" | sudo tee /etc/sysctl.d/60-custom.conf
echo "net.core.default_qdisc=fq" | sudo tee -a /etc/sysctl.d/60-custom.conf
echo "net.ipv4.tcp_congestion_control=bbr" | sudo tee -a /etc/sysctl.d/60-custom.conf
sudo sysctl -p /etc/sysctl.d/60-custom.conf
```


In this example, Im using Okta as a RADIUS server for 2FA.
In case of RADIUS, from Okta Im sending the following parameters
```
RADIUS attribute = 25 Class
Group memberships to return = [List of Okta group]
Response format = Repeating attributes
Group name format = ${group.name}
```

logs

```
tail -f /var/log/messages
```

## OpenConnect Documentation
* https://ocserv.gitlab.io/www/manual.html
* https://manpages.debian.org/stretch/ocserv/ocserv.8
* https://github.com/openconnect/ocserv/blob/master/doc/
* https://github.com/openconnect/ocserv/blob/master/doc/README-radius.md


## Okta Documentation

* https://help.okta.com/en-us/Content/Topics/integrations/radius-autopush.htm
* https://help.okta.com/en-us/Content/Topics/integrations/Agent_Installing_the_Okta_Radius_Agent.htm
* https://help.okta.com/en-us/Content/Topics/integrations/Agent_install_windows-config.htm
* https://help.okta.com/en-us/Content/Topics/integrations/cisco-radius-intg-test.htm

