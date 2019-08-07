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
- network:
    ethernets:
        enp0s3:
            addresses: [192.168.1.2/30]
            gateway4: 192.168.1.1
            nameservers:
              addresses: [8.8.8.8,8.8.4.4]
            dhcp4: no
    version: 2
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
