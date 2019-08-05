# Roger-Skyline

Resources: 
- https://openclassrooms.com/fr/courses/1561696-les-reseaux-de-zero/3199431-les-topologies
- https://openclassrooms.com/fr/courses/857447-apprenez-le-fonctionnement-des-reseaux-tcp-ip
- https://www.grafikart.fr/tutoriels/virtualxbox-debian-683

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
