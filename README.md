# Jarkom-Modul-3-IT05-2024

| Nama | NRP |
| ---- | :-: |
| Adlya Isriena Aftarisya | 5027231066 |
| Furqon Aryadana | 5027231024 |

## Topologi
<img width="577" alt="image" src="https://github.com/user-attachments/assets/adebc9ac-18fd-48f2-bfa7-3b326291015d">


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
	address 10.66.4.3
	netmask 255.255.255.0
	gateway 10.66.4.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Fritz (DNS Server)
```
auto eth0
iface eth0 inet static
	address 10.66.4.2
	netmask 255.255.255.0
	gateway 10.66.4.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Warhammer (Database Server)
```
auto eth0
iface eth0 inet static
	address 10.66.3.4
	netmask 255.255.255.0
	gateway 10.66.3.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Beast (Load Balancer Laravel)
```
auto eth0
iface eth0 inet static
	address 10.66.3.2
	netmask 255.255.255.0
	gateway 10.66.3.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Colossal (Load Balancer PHP)
```
auto eth0
iface eth0 inet static
	address 10.66.3.3
	netmask 255.255.255.0
	gateway 10.66.3.1
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
	address 10.66.2.2
	netmask 255.255.255.0
	gateway 10.66.2.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Eren (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 10.66.2.3
	netmask 255.255.255.0
	gateway 10.66.2.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Mikasa (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 10.66.2.4
	netmask 255.255.255.0
	gateway 10.66.2.1
  up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
### Zeke & Erwin (Client)
```
auto eth0
iface eth0 inet dhcp
```
## Soal 0
Perang antara kaum Marley dan Eldia telah mencapai puncak. Kaum Marley yang dipimpin oleh Zeke, me-register domain name **marley.yyy.com** untuk worker Laravel mengarah pada **Annie**. Namun ternyata tidak hanya kaum Marley saja yang berinisiasi, kaum Eldia ternyata sudah mendaftarkan domain name **eldia.yyy.com** untuk worker PHP (0) mengarah pada **Armin**.

### Setup DNS pada Fritz (DNS Server)
a. Instalasi dependencies yang diperlukan dan jalankan pada ```/root/.bashrc```
```
apt-get update
apt-get install bind9 -y
service bind9 start
```
b. Buat script pada ```fritz.bashrc```
```
forward="options {
	listen-on-v6 { none; };
	directory "/var/cache/bind";

	forwarders {
  	   192.168.122.1;
	};

	forward only;
	dnssec-validation no;

	auth-nxdomain no;
	allow-query{ any; };
};"

echo "$forward" > /etc/bind/named.conf.options

# Buat domain
echo 'zone "marley.it05.com" {
 	type master;
 	file "/etc/bind/it05/marley.it05.com";
};

zone "eldia.it05.com" {
 	type master;
 	file "/etc/bind/it05/eldia.it05.com";
};' > /etc/bind/named.conf.local

mkdir /etc/bind/it05

# config domain marley
echo '$TTL    604800
@       IN      SOA     marley.it05.com. root.marley.it05.com. (
                        2			; Serial
                        604800			; Refresh
                        86400			; Retry
                        2419200         	; Expire
                        604800 )		; Negative Cache TTL
;
@		IN      NS      marley.it05.com.
@		IN      A       10.66.1.2 ; IP Annie
www		IN      CNAME   marley.it05.com.' > /etc/bind/it05/marley.it05.com

# config domain eldia
echo'$TTL    604800
@       IN      SOA     eldia.it05.com. root.eldia.it05.com. (
                        2			; Serial
                        604800			; Refresh
                        86400			; Retry
                        2419200         	; Expire
                        604800 )		; Negative Cache TTL
;
@		IN      NS      eldia.it05.com.
@		IN      A       10.66.2.2 ; IP Armin
www		IN      CNAME   eldia.it05.com.' > /etc/bind/it05/eldia.it05.com

service bind9 restart
```

## Soal 1
Semua Client harus menggunakan konfigurasi ip address dari keluarga Tybur (dhcp)

### Setup DHCP Server (Tybur)
a. Instalasi dependencies yang diperlukan pada ```/root/.bashrc```
```
apt-get update
apt-get install isc-dhcp-server -y
service isc-dhcp-server start
```
b. Buat script `tybur.bashrc`
```
echo 'INTERFACES="eth0"' > /etc/default/isc-dhcp-server

echo 'subnet 10.66.1.0 netmask 255.255.255.0 {
	option routers 10.66.1.0;
	option broadcast-address 10.66.1.255;
	option domain-name-servers 10.66.4.2; #IP DNS Server
}
subnet 10.66.2.0 netmask 255.255.255.0 {
	option routers 10.66.2.0;
	option broadcast-address 10.66.2.255;
	option domain-name-servers 10.66.4.2; #IP DNS Server
}

subnet 10.66.3.0 netmask 255.255.255.0 {}

subnet 10.66.4.0 netmask 255.255.255.0 {}' > /etc/dhcp/dhcpd.conf
```

### Setup DHCP Relay (Paradis)
a. Instalasi dependencies yang diperlukan pada ```/root/.bashrc```
```
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start
```
b. Buat script `paradis.bashrc`
```
echo 'SERVERS="10.66.4.3" # IP DHCP Server
INTERFACES="eth1 eth2 eth3 eth4"
OPTIONS=""' > /etc/default/isc-dhcp-relay

echo 'net.ipv4.ip_forward=1' > /etc/sysctl.conf

service isc-dhcp-relay restart
```

## Soal 2 & 3
Client yang melalui bangsa **marley** mendapatkan range IP dari [prefix IP].1.05 - [prefix IP].1.25 dan [prefix IP].1.50 - [prefix IP].1.100. Sedangkan Client yang melalui bangsa **eldia** mendapatkan range IP dari [prefix IP].2.09 - [prefix IP].2.27 dan [prefix IP].2.81 - [prefix IP].2.243 

### Setup DHCP Server
Edit konfigurasi `subnet 10.66.1.0` dan `10.66.2.0` pada file `tybur.bashrc` menjadi seperti berikut:
```
subnet 10.66.1.0 netmask 255.255.255.0 {
	range 10.66.1.05 10.66.1.25;
	range 10.66.1.50 10.66.1.100;
	option routers 10.66.1.0;
	option broadcast-address 10.66.1.255;
}
subnet 10.66.2.0 netmask 255.255.255.0 {
	range 10.66.2.09 10.66.2.27;
	range 10.66.2.81 10.66.2.243;
	option routers 10.66.2.0;
	option broadcast-address 10.66.2.255;
}
```

### Testing
<img width="636" alt="image" src="https://github.com/user-attachments/assets/5fe780fd-ae48-4448-9e72-2832e23d21d8">
<img width="635" alt="image" src="https://github.com/user-attachments/assets/a082f13b-28a1-4952-8cc4-bfbdbb8be5f7">

## Soal 4
Client mendapatkan DNS dari keluarga Fritz dan dapat terhubung dengan internet melalui DNS tersebut

### Setup DHCP Server (Tybur)
Edit konfigurasi `subnet 10.66.1.0` dan `10.66.2.0` pada file `tybur.bashrc` menjadi seperti berikut:
```
subnet 10.66.1.0 netmask 255.255.255.0 {
	range 10.66.1.05 10.66.1.25;
	range 10.66.1.50 10.66.1.100;
	option routers 10.66.1.0;
	option broadcast-address 10.66.1.255;
	option domain-name-servers 10.66.4.2; # IP Fritz (DNS Server)
}
subnet 10.66.2.0 netmask 255.255.255.0 {
	range 10.66.2.09 10.66.2.27;
	range 10.66.2.81 10.66.2.243;
	option routers 10.66.2.0;
	option broadcast-address 10.66.2.255;
	option domain-name-servers 10.66.4.2; # IP Fritz (DNS Server)
}
```

### Testing
coba ping domain dari client

<img width="590" alt="image" src="https://github.com/user-attachments/assets/9b0cb4a3-b148-4614-95d1-5343ef6ed8eb">

<img width="576" alt="image" src="https://github.com/user-attachments/assets/9b9d92e0-b896-4b07-afad-6773853a8a14">

## Soal 5
Dikarenakan keluarga Tybur tidak menyukai kaum eldia, maka mereka hanya meminjamkan ip address ke kaum eldia selama **6 menit**. Namun untuk kaum marley, keluarga Tybur meminjamkan ip address selama **30 menit**. Waktu maksimal dialokasikan untuk peminjaman alamat IP selama **87 menit**.

### Setup DHCP Server (Tybur)
Edit konfigurasi `subnet 10.66.1.0` dan `10.66.2.0` pada file `tybur.bashrc` menjadi seperti berikut:
```
# Kaum Marley
subnet 10.66.1.0 netmask 255.255.255.0 {
	range 10.66.1.05 10.66.1.25;
	range 10.66.1.50 10.66.1.100;
	option routers 10.66.1.1;
	option broadcast-address 10.66.1.255;
	option domain-name-servers 10.66.4.2; # IP Fritz (DNS Server)
	default-lease-time 1800;
	max-lease-time 5220;
}

# Kaum Eldia
subnet 10.66.2.0 netmask 255.255.255.0 {
	range 10.66.2.09 10.66.2.27;
	range 10.66.2.81 10.66.2.243;
	option routers 10.66.2.1;
	option broadcast-address 10.66.2.255;
	option domain-name-servers 10.66.4.2; # IP Fritz (DNS Server)
	default-lease-time 360;
	max-lease-time 5220;
}
```

### Testing
Restart lalu buka web console pada client

a. Kaum Marley

<img width="565" alt="image" src="https://github.com/user-attachments/assets/5347ff5c-11aa-4b16-9c70-cec988ddb3f6">

b. Kaum Eldia

<img width="581" alt="image" src="https://github.com/user-attachments/assets/f1e53f69-af9f-4d3b-b7fc-53c20b639e2b">

## Soal 6
Armin berinisiasi untuk memerintahkan setiap worker PHP untuk melakukan konfigurasi virtual host untuk website berikut https://intip.in/BangsaEldia dengan menggunakan php 7.3

### Setup PHP Worker (Armin, Eren, Mikasa)

a. Instalasi dependencies yang diperlukan pada ```/root/.bashrc```
```
apt-get update
apt-get install lynx nginx wget unzip php7.3 php-fpm -y
```

b. Buat script `armin.bashrc`, `eren.bashrc`, `mikasa.bashrc`
```
# jalankan seervice php dan nginx
service php7.3-fpm start
service nginx start

# download resource website
wget --no-check-certificate 'https://drive.google.com/uc?export=download&id=1WhVXL6AejH-onwYpMClA6c_7WpQjY0Pr' -O /var/www/html/modul-3.zip
unzip /var/www/html/modul-3.zip -d /var/www/html/
mv /var/www/html/modul-3/* /var/www/html/
rm -r /var/www/html/modul-3

# tambahkan configurasi sites-available
echo 'server {
	listen 80;

	root /var/www/html;

	index index.php index.html index.htm;

	server_name _;

	location / {
		try_files \$uri \$uri/ /index.php?\$query_string;
	}

	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
	}

	error_log /var/log/nginx/jarkom-it05_error.log;
	access_log /var/log/nginx/jarkom-it05_access.log;
}' > /etc/nginx/sites-available/jarkom-it05.conf

ln -s /etc/nginx/sites-available/jarkom-it05.conf /etc/nginx/sites-enabled

rm /etc/nginx/sites-enabled/default

# restart service php dan nginx
service nginx restart
service php7.3-fpm restart
```
### Testing
```
lynx 10.66.2.1
```
<img width="798" alt="image" src="https://github.com/user-attachments/assets/7b063828-1f65-44a6-b085-6b77e336a935">

```
lynx 10.66.2.3
```
<img width="729" alt="image" src="https://github.com/user-attachments/assets/01b6ed6a-7169-435a-a259-2d926584ed98">

```
lynx 10.66.2.4
```
<img width="731" alt="image" src="https://github.com/user-attachments/assets/812809c3-595a-4bf0-906a-ac10491dc94c">

## Soal 7
Dikarenakan Armin sudah mendapatkan kekuatan titan colossal, maka bantulah kaum **eldia** menggunakan **colossal** agar dapat bekerja sama dengan baik. Kemudian lakukan testing dengan 6000 request dan 200 request/second.

### Setup Load Balancer (Colossal)
a. Instalasi dependencies yang diperlukan pada ```/root/.bashrc```
```
apt-get update
apt-get install lynx nginx wget unzip php7.3 php-fpm -y
apt-get install apache2-utils -y
```

b. Buat script `colossal.bashrc`
```
# jalankan seervice php dan nginx
service php7.3-fpm start
service nginx start

# tambahkan configurasi sites-available
echo 'upstream worker {
	server 10.66.2.2; # IP Armin
	server 10.66.2.3; # IP Eren
	server 10.66.2.4; # IP Mikasa
}

server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		proxy_pass http://worker;
	}
}' > /etc/nginx/sites-available/lb-it05.conf

ln -s /etc/nginx/sites-available/lb-it05.conf /etc/nginx/sites-enabled

rm /etc/nginx/sites-enabled/default

# restart service php dan nginx
service nginx restart
service php7.3-fpm restart
```

### Testing
```
ab -n 6000 -c 200 http://10.66.3.3/
```

<img width="671" alt="image" src="https://github.com/user-attachments/assets/804bfc78-a582-428e-98e2-b400ea840df2">

<img width="675" alt="image" src="https://github.com/user-attachments/assets/4432a655-9201-4403-a82a-e28904c8eb48">

## Soal 8
Karena Erwin meminta “laporan kerja Armin”, maka dari itu buatlah analisis hasil testing dengan **1000 request** dan **75 request/second** untuk masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
- Nama Algoritma Load Balancer
- Report hasil testing pada Apache Benchmark
- Grafik request per second untuk masing masing algoritma. 
- Analisis

### Penyelesaian
a. Ubah konfigurasi `upstream worker` pada `/etc/nginx/sites-available/lb-it05.conf` berdasarkan algoritma yang diinginkan
- Round Robin
  ```
  upstream worker {
	server 10.66.2.2; # IP Armin
	server 10.66.2.3; # IP Eren
	server 10.66.2.4; # IP Mikasa
  }
  ```
- Weighted Round Robin
  ```
  upstream worker {
	server 10.66.2.2 weight=4; # IP Armin
	server 10.66.2.3 weight=2; # IP Eren
	server 10.66.2.4 weight=1; # IP Mikasa
  }
  ```
- Least Connection
  ```
  upstream worker {
  	least_conn;
	server 10.66.2.2; # IP Armin
	server 10.66.2.3; # IP Eren
	server 10.66.2.4; # IP Mikasa
  }
  ```
- IP Hash
  ```
  upstream worker {
  	ip_hash;
	server 10.66.2.2; # IP Armin
	server 10.66.2.3; # IP Eren
	server 10.66.2.4; # IP Mikasa
  }
  ```
- Generic Hash
  ```
  upstream worker {
  	hash $request_uri consistent;
	server 10.66.2.2; # IP Armin
	server 10.66.2.3; # IP Eren
	server 10.66.2.4; # IP Mikasa
  }
  ```
b. Restart service nginx dan php-fpm
```
service nginx restart
service php7.3-fpm restart
```

### Testing
Lakukan load testing dengan Apache Benchmark pada IP load balancer (Colossal)
```
ab -n 1000 -c 75 http://10.66.3.3/
```
a. Round Robin

<img width="672" alt="image" src="https://github.com/user-attachments/assets/933660ee-b46b-430a-8e05-0f752b5ac217">

b. Weighted Round Robin

<img width="682" alt="image" src="https://github.com/user-attachments/assets/cdd45f84-a3e7-4fc7-b636-017a97fa5399">

c. Least Connection

<img width="671" alt="image" src="https://github.com/user-attachments/assets/a3b83f77-824e-458f-bae1-44ad928c9337">

d. IP Hash

<img width="673" alt="image" src="https://github.com/user-attachments/assets/ac0e8495-a1cd-4e13-b7c2-6bba09e48c8c">

e. Generic Hash

<img width="670" alt="image" src="https://github.com/user-attachments/assets/9ff00cc8-1b36-4544-b08e-ceb285fc87bd">

### Grafik Request Per Second masing-masing algoritma

<img width="521" alt="image" src="https://github.com/user-attachments/assets/da140f38-7068-4585-86c4-2efc3b72d0d0">

### Analisis

a. Round Robin
- **Request per second**: 1691.17
- **Time per second (Mean)**: 44.348 ms
- **Max time**: 130 ms
- **Analisis**: Round Robin menunjukkan performa yang cukup stabil, tetapi memiliki RPS terendah di antara algoritma lainnya. Distribusi yang tidak mempertimbangkan beban membuat algoritma ini kurang efisien dalam menangani jumlah permintaan yang besar.

b. Weighted Round Robin
- **Request per second**: 2543.14
- **Time per second (Mean)**: 29.491 ms
- **Max time**: 119 ms
- **Analisis**: Weighted Round Robin mendistribusikan lebih banyak permintaan ke server yang memiliki kapasitas lebih tinggi, menghasilkan peningkatan performa yang signifikan dibandingkan Round Robin standar. Algoritma ini mengurangi rata-rata waktu per permintaan dan memiliki RPS yang lebih tinggi, menjadikannya lebih efektif dalam lingkungan yang memiliki server dengan kapasitas yang berbeda.

c. Least Connection
- **Request per second**: 2483.15
- **Time per second (Mean)**: 30.204 ms
- **Max time**: 52 ms
- **Analisis**: Least Connection mengarahkan permintaan ke server dengan koneksi paling sedikit, sehingga meminimalkan kemungkinan adanya server yang kelebihan beban. Performanya mendekati Weighted Round Robin, dengan waktu permintaan maksimal yang lebih rendah. Algoritma ini cocok untuk aplikasi yang membutuhkan distribusi koneksi yang merata untuk menghindari penundaan.

d. IP Hash
- **Request per second**: 2106.82
- **Time per second (Mean)**: 35.599 ms
- **Max time**: 87 ms
- **Analisis**:  IP Hash mendistribusikan permintaan berdasarkan alamat IP klien, memberikan sesi yang konsisten bagi pengguna. Namun, performanya berada di bawah Weighted Round Robin dan Least Connection, meskipun lebih tinggi dari Round Robin. Algoritma ini lebih cocok untuk aplikasi yang membutuhkan session persistence, meskipun efisiensi dalam load balancing tidak setinggi algoritma lain.

e. Generic Hash
- **Request per second**: 2954.79
- **Time per second (Mean)**: 25.382 ms
- **Max time**: 79 ms
- **Analisis**:  Generic Hash memberikan hasil RPS tertinggi, menunjukkan bahwa algoritma ini paling efisien dalam mendistribusikan beban secara merata dan cepat. Dengan rata-rata waktu per permintaan yang terendah dan waktu permintaan maksimal yang lebih pendek, algoritma ini adalah yang paling optimal untuk menangani volume permintaan tinggi.

Kesimpulan:
- **Performa Tertinggi**: Generic Hash memberikan RPS tertinggi dan waktu per permintaan terendah, menjadikannya pilihan terbaik untuk skenario yang membutuhkan performa maksimal.
- **Pilihan Menengah**: Weighted Round Robin dan Least Connection menunjukkan performa yang baik untuk lingkungan dengan distribusi beban yang bervariasi.
- **Performa Terendah**: Round Robin memiliki performa yang paling rendah, lebih cocok untuk aplikasi dengan server yang kapasitasnya hampir sama.

## Soal 9
Dengan menggunakan algoritma **Least-Connection**, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak **1000 request** dengan **10 request/second**, kemudian tambahkan grafiknya pada “laporan kerja Armin”.

### Penyelesaian
a. Ubah konfigurasi `upstream worker` pada `/etc/nginx/sites-available/lb-it05.conf` dengan algoritma least connection, sesuaikan jumlah worker dengan jumlah yang ingin dilakukan testing
   ```
   upstream worker {
  	least_conn;
	server 10.66.2.2; # IP Armin
	server 10.66.2.3; # IP Eren
	server 10.66.2.4; # IP Mikasa
   }
   ```
b. Restart service nginx dan php-fpm
```
service nginx restart
service php7.3-fpm restart
```

### Testing
Lakukan load testing dengan Apache Benchmark pada IP load balancer (Colossal)
```
ab -n 1000 -c 10 http://10.66.3.3/
```
- 3 Worker
  
  <img width="673" alt="image" src="https://github.com/user-attachments/assets/a6df1ab0-8938-4f13-93d1-562f077a16a1">

- 2 Worker
  
  <img width="667" alt="image" src="https://github.com/user-attachments/assets/f947d1c9-516a-46d0-b971-a638002e54b7">

- 1 Worker
  
  <img width="663" alt="image" src="https://github.com/user-attachments/assets/30795a25-4ce6-49bf-ae5c-a8c6c66092fb">

### Grafik RPS
<img width="503" alt="image" src="https://github.com/user-attachments/assets/2bbb6949-29b7-46de-8fc0-74ebc4ef15ba">

### Analisis

a. 3 Worker
- **Request per second**: 2914.67
- **Time per second (Mean)**: 3.431 ms
- **Max time**: 60 ms
- **Analisis**: Dengan 3 worker, sistem mencapai RPS tinggi dan waktu respons cepat, meskipun ada beberapa permintaan yang memiliki waktu koneksi dan pemrosesan lebih lama (maksimum 60 ms). Dengan distribusi beban pada 3 worker, efisiensi meningkat karena permintaan yang besar dibagi rata, mengurangi beban pada tiap worker.

b. 2 Worker
- **Request per second**: 2803.12
- **Time per second (Mean)**: 3.567 ms
- **Max time**: 15 ms
- **Analisis**: Menggunakan 2 worker menunjukkan penurunan kecil dalam RPS dan peningkatan waktu rata-rata per permintaan dibandingkan dengan 3 worker. Namun, waktu terlama untuk permintaan lebih singkat (maksimum 15 ms). Ini menunjukkan bahwa dengan dua worker, sistem cenderung lebih stabil, tetapi kinerja secara keseluruhan sedikit berkurang.


c. 1 Worker
- **Request per second**: 2958.98
- **Time per second (Mean)**: 3.380 ms
- **Max time**: 25 ms
- **Analisis**: Dengan hanya satu worker, RPS meningkat sedikit dibandingkan dengan 2 worker. Namun, waktu terlama untuk satu permintaan meningkat menjadi 25 ms. Hal ini mungkin disebabkan oleh satu worker yang harus menangani semua permintaan sendiri, yang dapat membuat waktu pemrosesan lebih fluktuatif meskipun pada rata-rata permintaan lebih cepat.

Kesimpulan:
- **Efisiensi Maksimal**: Konfigurasi dengan 3 worker memberikan hasil yang paling stabil dengan jumlah permintaan per detik yang tinggi dan distribusi beban yang lebih merata, meskipun ada beberapa permintaan yang mencapai waktu koneksi dan pemrosesan lebih lama.
- **Stabilitas Baik**: 2 worker menawarkan performa yang stabil dengan waktu maksimal lebih rendah (15 ms), tetapi pada RPS sedikit lebih rendah dibandingkan 1 dan 3 worker.
- **RPS Tertinggi**: 1 worker memberikan RPS tertinggi, tetapi waktu maksimum lebih tinggi dibandingkan 2 worker, menunjukkan beban yang kurang merata di bawah satu worker.
  
## Soal 10
Selanjutnya coba tambahkan keamanan dengan konfigurasi autentikasi di Colossal dengan dengan kombinasi username: “arminannie” dan password: “jrkmyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/supersecret/

### Setup Load Balancer (Colossal)
Modifikasi script `colossal.bashrc`
```
# jalankan service php dan nginx
service php7.3-fpm start
service nginx start

# username dan password
mkdir -p /etc/nginx/supersecret
htpasswd -cb /etc/nginx/supersecret/htpasswd arminannie jrkmit05

# tambahkan configurasi sites-available
echo 'upstream worker {
	server 10.66.2.2; # IP Armin
	server 10.66.2.3; # IP Eren
	server 10.66.2.4; # IP Mikasa
}

server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		auth_basic "Restricted Content"; # konfigurasi autentikasi
        	auth_basic_user_file /etc/nginx/supersecret/htpasswd; # konfigurasi autentikasi
		proxy_pass http://worker;
	}
}' > /etc/nginx/sites-available/lb-it05.conf

ln -s /etc/nginx/sites-available/lb-it05.conf /etc/nginx/sites-enabled

rm /etc/nginx/sites-enabled/default

# restart service php dan nginx
service nginx restart
service php7.3-fpm restart
```

### Testing
<img width="589" alt="image" src="https://github.com/user-attachments/assets/070a02dc-8b3d-4d61-ae9f-f3e842ec6e87">

<img width="647" alt="image" src="https://github.com/user-attachments/assets/f6df7812-daca-43ae-83b4-922905802a6d">

<img width="422" alt="image" src="https://github.com/user-attachments/assets/f8776444-19ef-4d06-9467-e62bcf4ed3ba">

<img width="685" alt="image" src="https://github.com/user-attachments/assets/462b550c-cd05-401e-83e8-f1c3934767d1">

## Soal 11
Lalu buat untuk setiap request yang mengandung /titan akan di proxy passing menuju halaman https://attackontitan.fandom.com/wiki/Attack_on_Titan_Wiki

### Setup Load Balancer (Colossal)
a. Ubah konfigurasi `server` pada `/etc/nginx/sites-available/lb-it05.conf` 
   ```
   server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		auth_basic "Restricted Content"; # konfigurasi autentikasi
        	auth_basic_user_file /etc/nginx/supersecret/htpasswd; # konfigurasi autentikasi
		proxy_pass http://worker;
	}

  	location /titan {
		auth_basic "Restricted Content"; # konfigurasi autentikasi
        	auth_basic_user_file /etc/nginx/supersecret/htpasswd; # konfigurasi autentikasi
		proxy_pass https://attackontitan.fandom.com/wiki/Attack_on_Titan_Wiki;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto $scheme;
	}
   }
   ```
b. Restart service nginx dan php-fpm
```
service nginx restart
service php7.3-fpm restart
```

### Testing
```
lynx 10.66.3.3/titan
```
<img width="734" alt="image" src="https://github.com/user-attachments/assets/041d435f-4ad3-4ce6-8791-3302a82b6f38">


## Soal 12
Selanjutnya Colossal ini hanya boleh diakses oleh client dengan IP [Prefix IP].1.77, [Prefix IP].1.88, [Prefix IP].2.144, dan [Prefix IP].2.156.

### Setup Load Balancer (Colossal)
a. Ubah konfigurasi `server` pada `/etc/nginx/sites-available/lb-it05.conf` 
   ```
   server {
	listen 80;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		allow 10.66.1.77;
		allow 10.66.1.88;
		allow 10.66.2.144;
		allow 10.66.2.156;
		deny all;

		auth_basic "Restricted Content"; # konfigurasi autentikasi
        	auth_basic_user_file /etc/nginx/supersecret/htpasswd; # konfigurasi autentikasi
		proxy_pass http://worker;
	}

  	location /titan {
		auth_basic "Restricted Content"; # konfigurasi autentikasi
        	auth_basic_user_file /etc/nginx/supersecret/htpasswd; # konfigurasi autentikasi
		proxy_pass https://attackontitan.fandom.com/wiki/Attack_on_Titan_Wiki;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto $scheme;
	}
   }
   ```
b. Restart service nginx dan php-fpm
```
service nginx restart
service php7.3-fpm restart
```

### Testing
```
lynx 10.66.3.3
```
- Erwin
  
<img width="747" alt="image" src="https://github.com/user-attachments/assets/54cc5f7e-db51-4e02-bdef-80ce703b42c0">

- Zeke

<img width="728" alt="image" src="https://github.com/user-attachments/assets/100a703b-3ba7-4587-898f-4a5b18a0ac8b">

### Fix IP Client
modifikasi network configuration pada node Erwin
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 22:5d:5c:a6:9e:8b # MAC Address
```
Tambahkan konfigurasi berikut pada `/etc/dhcp/dhcpd.conf` di DHCP Server 
```
host Erwin {
    hardware ethernet 22:5d:5c:a6:9e:8b;
    fixed-address 10.66.2.144;
    option host-name "Erwin";
}
```
