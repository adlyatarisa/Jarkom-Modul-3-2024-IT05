# Jarkom-Modul-3-IT05-2024

| Nama | NRP |
| ---- | :-: |
| Adlya Isriena Aftarisya | 5027231066 |
| Furqon Aryadana | 5027231024 |

## Topologi
<img width="827" alt="image" src="https://github.com/user-attachments/assets/832ba78f-ce3f-405d-937f-13da6265e0d8">


## Network Configuration
### Paradis (Router / DHCP Relay)
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.66.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.66.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.66.3.1
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 10.66.4.1
	netmask 255.255.255.0

up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
agar setiap node yang ada dalam jaringan dapat mengakses internet luar. Untuk itu, tambahkan command berikut pada ```/root/.bashrc```:
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.66.0.0/16
```
### Tybur (DHCP Server)
```
auto eth0
iface eth0 inet static
	address 10.66.3.3
	netmask 255.255.255.0
	gateway 10.66.3.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Fritz (DNS Server)
```
auto eth0
iface eth0 inet static
	address 10.66.3.2
	netmask 255.255.255.0
	gateway 10.66.3.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Warhammer (Database Server)
```
auto eth0
iface eth0 inet static
	address 10.66.2.4
	netmask 255.255.255.0
	gateway 10.66.2.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Beast (Load Balancer Laravel)
```
auto eth0
iface eth0 inet static
	address 10.66.2.2
	netmask 255.255.255.0
	gateway 10.66.2.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Colossal (Load Balancer PHP)
```
auto eth0
iface eth0 inet static
	address 10.66.2.3
	netmask 255.255.255.0
	gateway 10.66.2.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Annie (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 10.66.1.2
	netmask 255.255.255.0
	gateway 10.66.1.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Bertholdt (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 10.66.1.3
	netmask 255.255.255.0
	gateway 10.66.1.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Reiner (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 10.66.1.4
	netmask 255.255.255.0
	gateway 10.66.1.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Armin (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 10.66.4.2
	netmask 255.255.255.0
	gateway 10.66.4.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Eren (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 10.66.4.3
	netmask 255.255.255.0
	gateway 10.66.4.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Mikasa (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 10.66.4.4
	netmask 255.255.255.0
	gateway 10.66.4.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Zeke & Erwin (Client)
```
auto eth0
iface eth0 inet dhcp
```



