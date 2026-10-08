# DHCP Practice

## 1. Infraestructura

Máquinas virtuales:

- dhcp
- c1
- printer

Red interna:

192.168.57.0/24

Servidor DHCP:

192.168.57.10/24

## 2. Instalación DHCP
Instalación del servicio:
sudo apt update
sudo apt install isc-dhcp-server

Configuración de la interfaz:
sudo nano /etc/default/isc-dhcp-server
INTERFACESv4="eth2"

Copia de seguridad:
sudo cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.bak

ip a para comprobar

## 3. Configuración del rango DHCP

Archivo de configuración:
sudo nano /etc/dhcp/dhcpd.conf

Configuración:

ddns-update-style none;

option domain-name "nuria.test";
option domain-name-servers 10.0.0.2, 4.4.4.4;

default-lease-time 86400;
max-lease-time 691200;

subnet 192.168.57.0 netmask 255.255.255.0 {
    range 192.168.57.20 192.168.57.50;
    option routers 192.168.57.10;
}

Comprobación de sintaxis:
sudo dhcpd -t

Estado del servicio:
sudo systemctl status isc-dhcp-server.service

Puerto DHCP:
sudo ss -lun

Logs:
sudo cat /var/log/syslog | grep dhcpd

## 4. 

Comprobaciones, mensajes DHCP:
DHCPDISCOVER
DHCPOFFER
DHCPREQUEST
DHCPACK

## 5. Máquina c1

Entrada en la máquina:
vagrant ssh c1

Comprobar interfaces:
ip a

Dirección obtenida:
192.168.57.22/24

Comprobar rutas:
ip r

sudo dhclient -r
sudo dhclient

Comprobar de nuevo la dirección:
ip a

La dirección obtenida está dentro del rango DHCP:
192.168.57.20 - 192.168.57.50

sudo cat /var/lib/dhcp/dhcpd.leases

## 6. Máquina printer

Entrada en la máquina:
vagrant ssh printer

Comprobar interfaces:
ip a

Dirección :
192.168.57.100/24

ip r

sudo nano /etc/dhcp/dhcpd.conf

Configuración:

host printer {
    hardware ethernet 08:00:27:75:f0:4f;
    fixed-address 192.168.57.100;
    default-lease-time 7200;
}

Comprobación de sintaxis:
sudo dhcpd -t

Comprobación:
sudo systemctl status isc-dhcp-server.service

Comprobar la dirección de "printer":
ip a

Resultado:
192.168.57.100/24

## 7. Routing

Activar el reenvío IPv4:
echo "1" > /proc/sys/net/ipv4/ip_forward

Configuración permanente:
sudo nano /etc/sysctl.conf

net.ipv4.ip_forward=1

Comprobación:
cat /proc/sys/net/ipv4/ip_forward

Configurar NAT:
sudo iptables -t nat -A POSTROUTING -s 192.168.57.0/24 -o eth0 -j MASQUERADE

## 8. Comprobaciones finales
c1:
ip a
ip r

Resultado:
192.168.57.22/24

printer:
ip a
ip r

Resultado:
192.168.57.100/24

## 9. Git

Inicialización:
git init
git commit --allow-empty -m "Initial commit: Setup DHCP Practice repository"

Añadir los archivos:
git add .

Crear commit :
git commit -m "practica-DHCP"

git remote add origin https://github.com/nlecmun-stack/DHCP.git

alumnom@a209e21:~/Escritorio/practicas-sri/DHCP$ git remote -v
origin	https://github.com/nlecmun-stack/DHCP.git (fetch)
origin	https://github.com/nlecmun-stack/DHCP.git (push)

git push
user-name: nlecmun-stack
password: Personal Access Token de GitHub