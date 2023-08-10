# ocserv

## Install on Rocky, Tested on Rocky 9.2


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
mkdir -p /var/www/ocserv
chown ocserv:ocserv /var/www/ocserv -R
certbot certonly --webroot --agree-tos --email you@exmaple.com -d vpn.example.com -w /var/www/ocserv
```

Choose Authentication Method
```
PAM, Authentication File, RADIUS, etc
```
Authentication File Example
```
auth = "plain[/etc/ocserv/ocpasswd]"
```
Add New Local User
```
ocpasswd -c /etc/ocserv/ocpasswd username
```

Lock User
```
ocpasswd -c /etc/ocserv/ocpasswd -l username
```

Unlock User

```
ocpasswd -c /etc/ocserv/ocpasswd -u username
```

Delete User
```
ocpasswd -c /etc/ocserv/ocpasswd -d username
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

In case of Masquerading, maybe this commands will help you
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

# occtl
```
[root@vpn conf.d]# occtl
OpenConnect server control (occtl) version 1.2.0
Copyright (C) 2014-2017 Red Hat and others.
ocserv comes with ABSOLUTELY NO WARRANTY. This is free software,
and you are welcome to redistribute it under the conditions of the
GNU General Public License version 2.

For help type ? or 'help'
==================================================================
> ?
Available Commands
 disconnect user [NAME] Disconnect the specified user
 disconnect id [ID]     Disconnect the specified ID
     unban ip [IP]      Unban the specified IP
           reload       Reloads the server configuration
      show status       Prints the status and statistics of the server
       show users       Prints the connected users
     show ip bans       Prints the banned IP addresses
 show ip ban points     Prints all the known IP addresses which have points
     show iroutes       Prints the routes provided by users of the server
 show sessions all      Prints all the session IDs
 show sessions valid    Prints all the valid for reconnection sessions
 show session [SID]     Prints information on the specified session
    show user [NAME]    Prints information on the specified user
      show id [ID]      Prints information on the specified ID
      show events       Provides information about connecting users
         stop now       Terminates the server
            reset       Resets the screen and terminal
         help or ?      Prints this help
             exit       Exits this application


```

# rpm
```
[root@vpn conf.d]# rpm -ql ocserv-1.2.0-1.el9.x86_64
/etc/ocserv
/etc/ocserv/ocserv.conf
/etc/pam.d/ocserv
/usr/bin/occtl
/usr/bin/ocpasswd
/usr/bin/ocserv-fw
/usr/bin/ocserv-script
/usr/lib/.build-id
/usr/lib/.build-id/06
/usr/lib/.build-id/06/6521018d187b63051e2ecefb26c7aa8adcc72b
/usr/lib/.build-id/39
/usr/lib/.build-id/39/384b39e82ea0c5a9f93716ea772854fa3db3b3
/usr/lib/.build-id/50
/usr/lib/.build-id/50/1eefa2a5c896a2628d30322a92d528a69d9a8d
/usr/lib/.build-id/b1
/usr/lib/.build-id/b1/12b541fc648ba6069189436cd9ff94a4bc6946
/usr/lib/systemd/system/ocserv.service
/usr/sbin/ocserv
/usr/sbin/ocserv-genkey
/usr/sbin/ocserv-worker
/usr/share/doc/ocserv
/usr/share/doc/ocserv/AUTHORS
/usr/share/doc/ocserv/BSD-MIT
/usr/share/doc/ocserv/CC0
/usr/share/doc/ocserv/COPYING
/usr/share/doc/ocserv/ChangeLog
/usr/share/doc/ocserv/LGPL-2.1
/usr/share/doc/ocserv/NEWS
/usr/share/doc/ocserv/PACKAGE-LICENSING
/usr/share/doc/ocserv/README-radius.md
/usr/share/doc/ocserv/README.md
/usr/share/man/man8/occtl.8.gz
/usr/share/man/man8/ocpasswd.8.gz
/usr/share/man/man8/ocserv.8.gz
/var/lib/ocserv
/var/lib/ocserv/profile.xml

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
* https://help.okta.com/en-us/Content/Topics/Directory/Directory_AD_Field_Mappings.htm

