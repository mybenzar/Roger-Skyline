# Roger-Skyline

Resources: 
- https://openclassrooms.com/fr/courses/1561696-les-reseaux-de-zero/3199431-les-topologies
- https://openclassrooms.com/fr/courses/857447-apprenez-le-fonctionnement-des-reseaux-tcp-ip
- https://www.grafikart.fr/tutoriels/virtualxbox-debian-683
- https://crontab.guru/

# Port Forwarding : 
- Networks > Advanced > Port Forwarding
- Set host to 127.0.0.1 / 2222 and guest to 10.0.1.15 / 22
- Create new user and establish a connection with this user

# Static Ip :
- sudo vi /etc/netplan/*.yaml
```
network:
    ethernets:
        enp0s8:
            addresses: [10.0.0.1/30]
            gateway4: 10.11.254.254
            nameservers:
              addresses: [10.11.254.254,8.8.8.8]
            dhcp4: no
        enp0s3:
            dhcp4: yes
    version: 2
```
- save changes : sudo netplan apply

# Restart ssh : 
sudo service ssh restart

# Create key ssh : 
- generate from local machine : ssh-keygen -t rsa -b 4096 -C "mail@mail.fr"
- when connecting to vm : ssh-copy-id guest@localhost -p 2222
Alternative:
- generate from local machine : ssh-keygen -t rsa -b 4096 -C "mail@mail.fr"
- create .ssh dir with special rights in vm : mkdir -m 700 .ssh 
- connect to virtual machine, coppy the key in .ssh/id_rsa.pub (local) to .ssh/authorized_keys (vm) 
- change rights for the file : chmod 600 .ssh/authorized_keys

# Set-Up Cron : 
- crontab -e

# Firewall
- display listening ports : netsat --inet -npl
- show iptables rules that are currently applied : sudo iptables -L
- create sh file with all rules :
``` sh
#!/bin/bash

# Flush
iptables -F
iptables -X

# Policies
iptables -P OUTPUT DROP
iptables -P INPUT DROP
iptables -P FORWARD DROP

# To keep established connections
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Enable Ping
iptables -A INPUT -p icmp -j ACCEPT
iptables -A OUTPUT -p icmp -j ACCEPT

# Enable Loopback
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# Open SSH port so we can access it from outside
iptables -A INPUT -p tcp --dport 2222 -j ACCEPT

# DNS
iptables -A INPUT -p tcp --dport 53 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 53 -j ACCEPT
iptables -A INPUT -p udp --dport 53 -j ACCEPT
iptables -A OUTPUT -p udp --dport 53 -j ACCEPT

# HTTP
#iptables -A INPUT -p tcp --dport 80 -j ACCEPT
#iptables -A OUTPUT -p tcp --dport 80 -j ACCEPT

# HTTPS
#iptables -A INPUT -p tcp --dport 443 -j ACCEPT
#iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT

```
- remove 'sh' extension
- chmod +x firewall
- put firewall in init.d
```
sudo apt-get install -y iptables-persistent
```
check out tutorial for Ubuntu 18.04 : https://linuxconfig.org/how-to-make-iptables-rules-persistent-after-reboot-on-linux
basically, once the iptables changes have been applied:
```
apt-get install iptables-persistent -y
apt-get install netfilter-persistent -y
iptables-save > /etc/iptables/rules.v4
netfilter-persistent save
```
# DOS protection
```
apt-get install fail2ban
```
```
cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

```
in /etc/fail2ban/jail.local, add : 
```
[sshd]
enabled     = true
port        = ssh
filter      = sshd
action      = iptables[name=SSH, port=2222, protocol=tcp]
ignoreip    = 10.0.2.2
logpath     = /var/log/auth.log
maxretry    = 3
bantime     = 1800
```
comment sshd rules
useful commands for fail2ban
- to check who tried to connect and how many times (jail)
```
fail2ban-client status
```
# Ports scan protection
```
apt-get install portsentry
```
```
vi /etc/default/portsentry
```
```
TCP_MODE="atcp"
UDP_MODE="audp"
```
```
vi /etc/portsentry/portsentry.conf
```
```
BLOCK_UDP="1"
BLOCK_TCP="1"
uncomment KILL_ROUTE="/sbin/iptables -I ..."
```
```
service portsentry restart
```
# Other useful commands
```
service [service_name] status
service [service_name] restart
service [service_name] start
```
