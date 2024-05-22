# **LAPORAN RESMI PRAKTIKUM KOMUNIKASI DATA & JARINGAN KOMPUTER MODUL 3**

Berikut adalah Laporan Resmi Praktikum Komunikasi Data & Jaringan Komputer Modul 3 oleh Kelompok IT18.

| Nama                 | NRP        |
| -------------------- | ---------- |
| Iki Adfi Nur Mohamad | 5027221033 |
| Siti Nur Ellyzah     | 5027221014 |
---

## Daftar Isi

- [Topologi](#topologi)
- [Konfigurasi](#konfigurasi)

0. [Soal 0 - 1](#soal-0---1)
1. [Soal 2 - 5](#Soal-2---5)
2. [Soal 6](#soal-6)
3. [Soal 7](#soal-7)
4. [Soal 8](#soal-8)
5. [Soal 9](#soal-9)
6. [Soal 10](#soal-10)
7. [Soal 11](#Soal-11)
8. [Soal 12](#Soal-12)
9. [Soal 13](#Soal-13)
10. [Soal 14](#Soal-14)
11. [Soal 15](#Soal-15)
12. [Soal 16](#Soal-16)
13. [Soal 17](#Soal-17)
14. [Soal 18](#Soal-18)
15. [Soal 19](#Soal-19)
16. [Soal 20](#Soal-20)

- [Kendala](#kendala)
- [Notes](#Notes)

---

# Topologi
![topologi](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/263daa4a-e898-485b-bfc0-02b04ec51de9)

---

# Konfigurasi
- Arakis (Router (DHCP Relay))
```
auto eth0
iface eth0 inet dhcp
up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.242.0.0/16

auto eth1
iface eth1 inet static
	address 192.242.1.0
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.242.2.0
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.242.3.0
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 192.242.4.0
	netmask 255.255.255.0
```
- Mohiam (DHCP Server)
```
auto eth0
iface eth0 inet static
	address 192.242.3.1
	netmask 255.255.255.0
	gateway 192.242.3.0
```
- Irulan (DNS Server)
```
auto eth0
iface eth0 inet static
	address 192.242.3.2
	netmask 255.255.255.0
	gateway 192.242.3.0
```
- Chani (Database Server)
```
auto eth0
iface eth0 inet static
	address 192.242.4.1
	netmask 255.255.255.0
	gateway 192.242.4.0
```
- Stilgar (Load Balancer)
```
auto eth0
iface eth0 inet static
	address 192.242.4.2
	netmask 255.255.255.0
	gateway 192.242.4.0
```
- Leto (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 192.242.2.2
	netmask 255.255.255.0
	gateway 192.242.2.0
```
- Duncan (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 192.242.2.3
	netmask 255.255.255.0
	gateway 192.242.2.0
```
- Jessica (Laravel Worker)
```
auto eth0
iface eth0 inet static
	address 192.242.2.4
	netmask 255.255.255.0
	gateway 192.242.2.0
```
- Vladimir (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 192.242.1.2
	netmask 255.255.255.0
	gateway 192.242.1.0
```
- Rabban (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 192.242.1.3
	netmask 255.255.255.0
	gateway 192.242.1.0
```
- Feyd (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 192.242.1.4
	netmask 255.255.255.0
	gateway 192.242.1.0
```
- Dmitri & Paul (Client)
```
auto eth0
iface eth0 inet dhcp
```
---

## **Soal 0 - 1**
> Planet Caladan sedang mengalami krisis karena kehabisan spice, klan atreides berencana untuk melakukan eksplorasi ke planet arakis dipimpin oleh duke leto mereka meregister domain name atreides.yyy.com untuk worker Laravel mengarah pada Leto Atreides . Namun ternyata tidak hanya klan atreides yang berusaha melakukan eksplorasi, Klan harkonen sudah mendaftarkan domain name harkonen.yyy.com untuk worker PHP (0) mengarah pada Vladimir Harkonen

> (1) Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.

- Sebelum ini kita bisa melakukan setup untuk konfigurasi DHCP Server di Arakis, untuk ini bisa dibuat dan input dalam script berikut :

**script.sh**
```bash
echo nameserver 192.168.122.1 > /etc/resolv.conf
echo /etc/resolv.conf
```

- Setelahnya jalankan dengan command `./script.sh` dan jalankan script `relay.sh` yang berisikan dependensi.

**relay.sh**
```
apt-get update
apt-get install isc-dhcp-relay -y
```

- Gunakan `./relay.sh`, akan muncul tampilan seperti ini maka bisa diisi dengan IP DHCP yaitu IP dari Mohiam `192.242.3.1`.

![relay_1](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/f92c4988-5a01-47e9-b874-7192902215ec)

Untuk ini bisa isi dengan `eth1 eth2 eth3`

![relay_2](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/a1fc0886-fe00-4aaa-914b-e11bff65bc63)

Dan ini bisa lanjut klik `Enter`

![relay_3](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/a0a21721-c10c-43d8-9888-3641db989fea)

- Selanjutnya jalankan `sysctl.sh` yang berisikan konfigurasi dari Arakis untuk Mohiam (DHCP Server) dengan command `./sysctl.sh`. 

**sysctl.sh**
```bash
#!/bin/bash

cat > /etc/default/isc-dhcp-relay << EOL
SERVERS="192.242.3.1" #IP Mohiam (DHCP Server)
INTERFACES="eth1 eth2 eth3"
OPTIONS=
EOL

cat > /etc/sysctl.conf << EOL
    echo net.ipv4.ip_forward=1
EOL

service isc-dhcp-relay restart
```

Maka akan muncul output seperti ini :

![sysctl](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/0f8a1b5f-892c-4954-b650-659684419351)

- Setelah setup Arakis kita akan setup config ke Irulan yang bertugas sebagai DNS Server.

- Buat `script.sh` kembali.

**script.sh**
```bash
#!/bin/bash

echo nameserver 192.168.122.1 > /etc/resolv.conf
cat > /etc/resolv.conf
```
- Jalankan `script.sh` dan buat konfigurasi baru yaitu `scriptNamed.sh`.

**scriptNamed.sh**
```
apt-get update
apt-get install bind9 -y

forward="options {
directory \"/var/cache/bind\";
forwarders {
  	   192.168.122.1;
};

allow-query{any;};
listen-on-v6 { any; };
};
"
echo "$forward" > /etc/bind/named.conf.options

echo "zone \"atreides.it18.com\" {
	type master;
	file \"/etc/bind/jarkom/atreides.it18.com\";
};

zone \"harkonen.it18.com\" {
	type master;
	file \"/etc/bind/jarkom/harkonen.it18.com\";
};
" > /etc/bind/named.conf.local

mkdir /etc/bind/jarkom

atreides="
;
;BIND data file for local loopback interface
;
\$TTL    604800
@    IN    SOA    atreides.it18.com. root.atreides.it18.com. (
        2        ; Serial
                604800        ; Refresh
                86400        ; Retry
                2419200        ; Expire
                604800 )    ; Negative Cache TTL
;                   
@    IN    NS    atreides.it18.com.
@       IN    A    192.242.2.1  ; IP LETO
"
echo "$atreides" > /etc/bind/jarkom/atreides.it18.com

harkonen="
;
;BIND data file for local loopback interface
;
\$TTL    604800
@    IN    SOA    harkonen.it18.com. root.harkonen.it18.com. (
        2        ; Serial
                604800        ; Refresh
                86400        ; Retry
                2419200        ; Expire
                604800 )    ; Negative Cache TTL
;                   
@    IN    NS    harkonen.it18.com.
@       IN    A    192.242.1.1  ; IP VLADIMIR
"
echo "$harkonen" > /etc/bind/jarkom/harkonen.it18.com

service bind9 restart
```

- Jalankan `./scriptNamed.sh`, setelahnya bisa cek ping `atreides.it18.com` dan `harkonen.it18.com` di Client Paul / Vladimir, apabila berhasil maka akan tampil hasil ping sebagai berikut :

![nomor0_1](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/686df21f-6c17-4cfc-966f-05b8a82625f8)
![nomor0_2](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/4b9ef8ea-b638-47a1-8b04-2c6814c60f83)

---

## **Soal 2 - 5**
> Kemudian, karena masih banyak spice yang harus dikumpulkan, bantulah para aterides untuk bersaing dengan harkonen dengan kriteria berikut.
> - Semua CLIENT harus menggunakan konfigurasi dari DHCP Server.
> - Client yang melalui House Harkonen mendapatkan range IP dari [prefix IP].1.14 - [prefix IP].1.28 dan [prefix IP].1.49 - [prefix IP].1.70 (2)
> - Client yang melalui House Atreides mendapatkan range IP dari [prefix IP].2.15 - [prefix IP].2.25 dan [prefix IP].2 .200 - [prefix IP].2.210 (3)
> - Client mendapatkan DNS dari Princess Irulan dan dapat terhubung dengan internet melalui DNS tersebut (4)
> - Durasi DHCP server meminjamkan alamat IP kepada Client yang melalui House Harkonen selama 5 menit sedangkan pada client yang melalui House Atreides selama 20 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 87 menit (5)
**_*house == switch_**

- Untuk soal ini pertama-tama lakukan setup konfigurasi di Mohiam (DHCP Server). Buat script baru `setupSubnet.sh`.
Script ini berisi instalasi dependensi dibagian `apt-get` dan interfaces v4 dari `eth0`.

**setupSubnet.sh**
```bash
#!/bin/bash

apt-get update
apt-get install isc-dhcp-server -y

cat >/etc/default/isc-dhcp-server <<EOL
    echo INTERFACESv4="eth0"
EOL

cat >/etc/dhcp/dhcpd.conf <<EOL
    option domain-name "example.org";
option domain-name-servers ns1.example.org, ns2.example.org;

ddns-update-style none;

subnet 192.242.1.0 netmask 255.255.255.0 {
    range 192.242.1.14 192.242.1.28;
    range 192.242.1.49 192.242.1.70;
    option routers 192.242.1.0;
    option broadcast-address 192.242.1.255;
    option domain-name-servers 192.242.3.2;
    default-lease-time 300;
    max-lease-time 5220; 
}

subnet 192.242.2.0 netmask 255.255.255.0 {
    range 192.242.2.15 192.242.2.25;
    range 192.242.2.200 192.242.2.210;
    option routers 192.242.2.0;
    option broadcast-address 192.242.2.255;
    option domain-name-servers 192.242.3.2;
    default-lease-time 1200;
    max-lease-time 5220;
}

subnet 192.242.3.0 netmask 255.255.255.0 {
    
}

subnet 192.242.4.0 netmask 255.255.255.0 {
    
}

# host Paul {
#     hardware ethernet de:51:76:82:95:6b;
#     fixed-address 192.242.1.37;
# }

EOL

service isc-dhcp-server stop
service isc-dhcp-server start
```

- Kemudian untuk Nomor 2, terletak pada bagian `subnet 192.242.1.0 netmask 255.255.255.0` dengan permintaan range dan waktu dari soal.

- Sama seperti Nomor 3, terletak pada bagian `subnet 192.242.2.0 netmask 255.255.255.0` dengan permintaan range dan waktu dari soal.

- Jalankan `setupSubnet.sh` dan akan muncul output seperti ini.

![nomor5](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/061f587f-c139-435a-8cbb-a113d2c5d8b7)

- Untuk pengecekan apakah sudah terhubung, bisa ping dari Client (Paul / Dmitri)

- Setup Client Paul & Dmitri

![setupPaulDmitri](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/4cd0d734-df64-43f0-a820-565d25b0fa3c)

192.242.3.2 adalah IP Irulan

- Hasil ping Client Paul

![nomor5Paul](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/3e8a916e-1f50-4954-9ba9-973d693b1e3d)
![nomor5Paul_2](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/19d99a10-0497-4460-9561-f57511778595)

- Hasil ping Client Dmitri

![nomor5Dmitri](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/26018f0f-a1f9-4871-a26f-e9c34b0e61dd)
![nomor5Dmitri_2](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/cea7437f-87df-4c83-b992-9f7e0eb22cae)

---

## **Soal 6**
> Seiring berjalannya waktu kondisi semakin memanas, untuk bersiap perang. Klan Harkonen melakukan deployment sebagai berikut : 
> - Vladimir Harkonen memerintahkan setiap worker(harkonen) PHP, untuk melakukan konfigurasi virtual host untuk website berikut dengan menggunakan php 7.3. (6)

- Untuk soal ini kita bisa setup konfigurasi di Vladimir dengan `script.sh` dengan menggunakan command `nano /root/.bashrc`.

```
echo nameserver 192.242.3.2 > /etc/resolv.conf

apt-get update
apt-get install nginx -y
apt-get install lynx -y
apt-get install php php-fpm -y
apt-get install wget -y
apt-get install unzip -y
service nginx start
service php7.3-fpm start

wget -O '/var/www/harkonen.it18.com' 'https://drive.usercontent.google.com/u/0/uc?id=1lmnXJUbyx1JDt2OA5z_1dEowxozfkn30&export=download'
unzip -o /var/www/harkonen.it18.com -d /var/www/
rm /var/www/harkonen.it18.com
mv /var/www/modul-3 /var/www/harkonen.it18.com

source /root/script.sh
/root/script.sh
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/harkonen.it18.com
ln -s /etc/nginx/sites-available/harkonen.it18.com /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default

echo 'server {
     listen 80;
     server_name _;

     root /var/www/harkonen.it18.com;
     index index.php index.html index.htm;

     location / {
         try_files $uri $uri/ /index.php?$query_string;
     }

     location ~ \.php$ {
         include snippets/fastcgi-php.conf;
         fastcgi_pass unix:/run/php/php7.3-fpm.sock;
         fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
         include fastcgi_params;
     }
 }' > /etc/nginx/sites-available/harkonen.it18.com

 service nginx restart
```
192.242.3.2 adalah IP Irulan, bagian `apt-get` adalah fungsi dependensi, bagian `wget` merupakan proses download zip dan instalasi konfigurasi virtual host untuk *php 7.3* sesuai pada permintaan soal, bagian `source /root/script.sh` merupakan penggantian default menjadi konfigurasi sesuai file yang sudah didownload sebelumnya dan terakhir yang adalah bagian dari isi server.

- Selanjutnya jalankan script tersebut dengan `source /root/.bashrc`.

- Dan testing dengan `lynx localhost`, apabila muncul tampilan seperti ini maka sudah berhasil.

![localhostVladimir](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/ebaa1133-2a9c-4cb5-aad3-18e8ce4f4be2)

- Untuk PHP Worker Rabban dan Feyd lakukan setup konfigurasi yang sama. Setelah ditest apabila tampilan seperti ini maka sudah berhasil.

![localhostRabban](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/39537c02-3f1a-462e-bbb9-270ee9bb2379)
![localhostFeyd](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/8c4a984b-5508-4f10-90a0-1908511a8a74)

---

## **Soal 7**
> Aturlah agar Stilgar dari Freemen dapat dapat bekerja sama dengan maksimal, lalu lakukan testing dengan 5000 request dan 150 request/second. (7)

- Untuk nomor ini lakukan setup konfigurasi pada Stilgar (Load Balancer) dan Client.

- Setup Stilgar, jalankan `script.sh` dan `setup.sh`.

**script.sh**
```bash
#!/bin/bash

echo nameserver 192.168.122.1 > /etc/resolv.conf
echo nameserver 192.242.3.2   > /etc/resolv.conf # IP Irulan (DNS Server)
```

**setup.sh**
```
apt-get update
apt-get install apache2-utils -y
apt-get install nginx -y
apt-get install lynx -y
```

- Setelahnya, lanjut untuk setup di `.bashrc` untuk `stilgar.sh`.

**stilgar.sh**
```
# Salin file konfigurasi default ke lb_php
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

# Tambahkan konfigurasi load balancing ke dalam lb_php
cat <<EOL > /etc/nginx/sites-available/lb_php
upstream worker {
    server 192.242.1.1;
    server 192.242.1.2;
    server 192.242.1.3;
}

server {
    listen 80;
    server_name harkonen.it18.com www.harkonen.it18.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://worker;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOL

# Aktifkan konfigurasi baru
ln -sf /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/

# Hapus konfigurasi default jika ada
if [ -f /etc/nginx/sites-enabled/default ]; then
    rm /etc/nginx/sites-enabled/default
fi

# Restart layanan Nginx untuk menerapkan perubahan
service nginx restart
```

- Jika muncul tampilan seperti ini maka bisa lanjut ke langkah selanjutnya.

![setupStilgar](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/be2ba8fa-6db7-4626-9a02-7b0148fc0b06)

- Karena DNS berpindah ke IP Load Balancer maka diperlukan penyesuaian untuk pemindahan di Irulan.

- Setup untuk Irulan dengan script baru `no7.sh`.

**no7.sh**
```
atreides="
;
;BIND data file for local loopback interface
;
\$TTL    604800
@    IN    SOA    atreides.it18.com. root.atreides.it18.com. (
        2        ; Serial
                604800        ; Refresh
                86400        ; Retry
                2419200        ; Expire
                604800 )    ; Negative Cache TTL
;
@    IN    NS    atreides.it18.com.
@       IN    A    192.242.4.2 ; IP STILGAR
"
echo "$atreides" > /etc/bind/jarkom/atreides.it18.com

harkonen="
;
;BIND data file for local loopback interface
;
\$TTL    604800
@    IN    SOA    harkonen.it18.com. root.harkonen.it18.com. (
        2        ; Serial
                604800        ; Refresh
                86400        ; Retry
                2419200        ; Expire
                604800 )    ; Negative Cache TTL
;
@    IN    NS    harkonen.it18.com.
@       IN    A    192.242.4.2 ; IP STILGAR
"
echo "$harkonen" > /etc/bind/jarkom/harkonen.it18.com


# RESTART

service bind9 restart
```

- Jalankan `no7.sh` di Irulan dan apabila muncul seperti ini maka setup sudah berhasil.

![no7](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/e4a78df1-8431-45e2-8ebf-d9a6bed6ab23)

- Selanjutnya akan di tes pada client untuk uji testing.

- Setup Dmitri, jalankan `script.sh` dan buat script `no7.sh` untuk install dependensi.

**no7.sh**
```
apt update
apt install lynx -y
apt install htop -y
apt install apache-utils -y
apt-get install jq -y
```

- Jalankan `no7.sh` dan berikan command `ab -n 5000 -c 150 http://harkonen.it18.com/` untuk request 5000 request dan 150 request/second. Hasil akan terlihat seperti dibawah ini.

![no7Dmitri](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/fd34678e-8e1a-4f0c-8551-f1d5b82eeb93)
![no7Paul](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/22e5d319-2721-4469-9a28-85ba47081629)

*nb: untuk hasil output `ab` nomor 7 - 9 sebetulnya akan kurang lebih sama persis, hanya berbeda pada permintaan request yang disesuaikan soal saja*

---

## **Soal 8**
> Karena diminta untuk menuliskan peta tercepat menuju spice, buatlah analisis hasil testing dengan 500 request dan 50 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
> a. Nama Algoritma Load Balancer
> b. Report hasil testing pada Apache Benchmark
> c. Grafik request per second untuk masing masing algoritma. 
> d. Analisis (8)

- Masih dengan konfigurasi yang kurang lebih sama dengan nomor 7, hanya berbeda pada konfigurasi sesuai algoritma yang perlu disesuaikan saja. Perubahan ini disimpan discript `bash.rc`.

- a. Algoritma Generic Hash
```
# Salin file konfigurasi default ke lb_php
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

# Tambahkan konfigurasi load balancing ke dalam lb_php
# GENERIC HASH
cat <<EOL > /etc/nginx/sites-available/lb_php
upstream worker {
    hash $request_uri consistent;
    server 192.242.1.1;
    server 192.242.1.2;
    server 192.242.1.3;
}

server {
    listen 80;
    server_name harkonen.it18.com www.harkonen.it18.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://worker;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOL

# Aktifkan konfigurasi baru
ln -sf /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/

# Hapus konfigurasi default jika ada
if [ -f /etc/nginx/sites-enabled/default ]; then
    rm /etc/nginx/sites-enabled/default
fi

# Restart layanan Nginx untuk menerapkan perubahan
service nginx restart
```

b. Algoritma IP Hash
```
# Salin file konfigurasi default ke lb_php
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

# Tambahkan konfigurasi load balancing ke dalam lb_php
# IP HASH
cat <<EOL > /etc/nginx/sites-available/lb_php
upstream worker {
    ip_hash;
    server 192.242.1.1;
    server 192.242.1.2;
    server 192.242.1.3;
}

server {
    listen 80;
    server_name harkonen.it18.com www.harkonen.it18.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://worker;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOL

# Aktifkan konfigurasi baru
ln -sf /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/

# Hapus konfigurasi default jika ada
if [ -f /etc/nginx/sites-enabled/default ]; then
    rm /etc/nginx/sites-enabled/default
fi

# Restart layanan Nginx untuk menerapkan perubahan
service nginx restart
```

c. Algoritma Least Connection
```
# Salin file konfigurasi default ke lb_php
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

# Tambahkan konfigurasi load balancing ke dalam lb_php
# Least
cat <<EOL > /etc/nginx/sites-available/lb_php
upstream worker {
    least_conn;
    server 192.242.1.1;
    server 192.242.1.2;
    server 192.242.1.3;
}

server {
    listen 80;
    server_name harkonen.it18.com www.harkonen.it18.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://worker;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOL

# Aktifkan konfigurasi baru
ln -sf /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/

# Hapus konfigurasi default jika ada
if [ -f /etc/nginx/sites-enabled/default ]; then
    rm /etc/nginx/sites-enabled/default
fi

# Restart layanan Nginx untuk menerapkan perubahan
service nginx restart
```

d. Algoritma Round Robin
```
# Salin file konfigurasi default ke lb_php
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

# Tambahkan konfigurasi load balancing ke dalam lb_php
# ROUND ROBIN
cat <<EOL > /etc/nginx/sites-available/lb_php
upstream worker {
    server 192.242.1.1;
    server 192.242.1.2;
    server 192.242.1.3;
}

server {
    listen 80;
    server_name harkonen.it18.com www.harkonen.it18.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://worker;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOL

# Aktifkan konfigurasi baru
ln -sf /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/

# Hapus konfigurasi default jika ada
if [ -f /etc/nginx/sites-enabled/default ]; then
    rm /etc/nginx/sites-enabled/default
fi

# Restart layanan Nginx untuk menerapkan perubahan
service nginx restart
```

- Jalankan command `ab -n 500 -c 50 http://harkonen.it18.com/` pada Client untuk 500 request dan 50 request/second.

- Apabila berhasil maka akan menampilkan output berikut : 

a. Hasil Testing Generic Hash

![generichash](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/38ee8449-5d61-48ca-b414-5d4f6e13e1f4)

b. Hasil Testing IP Hash

![iphash](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/4f3fc700-0247-492c-9cbc-8e8ec31d7e10)

c. Hasil Testing Least Connection

![least](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/6fc218e7-2a57-4d37-85aa-5253eb57660f)

d. Hasil Testing Round Robin

![roundrobin](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/fe86bcfc-3bb3-421d-bcb9-1fd4b1425e35)

- Untuk Grafik Request Per Second seperti diagram batang berikut.

![grafik](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/585e4113-68af-46c5-8843-fdec80082986)

- Analisis bisa dicek pada link yang tertera di [Notes](#notes)
---

## **Soal 9**
> Dengan menggunakan algoritma Least-Connection, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 1000 request dengan 10 request/second, kemudian tambahkan grafiknya pada peta. (9)

- Sesuai dengan step pada langkah nomor sebelumnya, perbedaannya hanya terdapat pada script konfigurasi saja.

a. Dengan 1 Worker
```
# Salin file konfigurasi default ke lb_php
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

# Tambahkan konfigurasi load balancing ke dalam lb_php
# Least
cat <<EOL > /etc/nginx/sites-available/lb_php
upstream worker {
    least_conn;
    server 192.242.1.1;
}

server {
    listen 80;
    server_name harkonen.it18.com www.harkonen.it18.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://worker;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOL

# Aktifkan konfigurasi baru
ln -sf /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/

# Hapus konfigurasi default jika ada
if [ -f /etc/nginx/sites-enabled/default ]; then
    rm /etc/nginx/sites-enabled/default
fi

# Restart layanan Nginx untuk menerapkan perubahan
service nginx restart
```

b. Dengan 2 Worker
```
# Salin file konfigurasi default ke lb_php
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

# Tambahkan konfigurasi load balancing ke dalam lb_php
# Least
cat <<EOL > /etc/nginx/sites-available/lb_php
upstream worker {
    least_conn;
    server 192.242.1.1;
    server 192.242.1.2;
}

server {
    listen 80;
    server_name harkonen.it18.com www.harkonen.it18.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://worker;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOL

# Aktifkan konfigurasi baru
ln -sf /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/

# Hapus konfigurasi default jika ada
if [ -f /etc/nginx/sites-enabled/default ]; then
    rm /etc/nginx/sites-enabled/default
fi

# Restart layanan Nginx untuk menerapkan perubahan
service nginx restart
```

c. Dengan 3 Worker
```
# Salin file konfigurasi default ke lb_php
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

# Tambahkan konfigurasi load balancing ke dalam lb_php
# Least
cat <<EOL > /etc/nginx/sites-available/lb_php
upstream worker {
    least_conn;
    server 192.242.1.1;
    server 192.242.1.2;
    server 192.242.1.3;
}

server {
    listen 80;
    server_name harkonen.it18.com www.harkonen.it18.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://worker;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOL

# Aktifkan konfigurasi baru
ln -sf /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/

# Hapus konfigurasi default jika ada
if [ -f /etc/nginx/sites-enabled/default ]; then
    rm /etc/nginx/sites-enabled/default
fi

# Restart layanan Nginx untuk menerapkan perubahan
service nginx restart
```

- Jalankan command `ab -n 1000 -c 10 http://harkonen.it18.com/` pada Client untuk 1000 request dan 10 request/second.

- Apabila berhasil maka akan menampilkan output berikut : 
a. Hasil Testing Dengan 1 Worker

![dengan1worker](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/bea50ff1-29cd-43d1-9a8c-bdbd93fc32ab)

b. Hasil Testing Dengan 2 Worker

![dengan2worker](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/bc9e137b-5929-4c74-bd54-84ea72872cd8)

c. Hasil Testing Dengan 3 Worker

![dengan3worker](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/a24a3e1e-d7a8-4cb3-8898-e35d3c8bfdc1)

- Berikut merupakan hasil untuk Grafik Request Per-Second Testing Worker

![grafik_2](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/8bbefde6-bf51-4f80-b95c-68cf69ec3f93)

---

## **Soal 10**
> Selanjutnya coba tambahkan keamanan dengan konfigurasi autentikasi di LB dengan dengan kombinasi username: “secmart” dan password: “kcksyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/supersecret/ (10)

- Dengan konfigurasi di Load Balancer yaitu Stilgar, jalankan `script.sh` dan `setup.sh`, serta buat script baru `no10.sh` yang berisikan pembuatan untuk username `secmart`.

**no10.sh**
```bash
#!/bin/bash

mkdir /etc/nginx/supersecret

htpasswd -c /etc/nginx/supersecret/htpasswd secmart
```

- Setelahnya akan muncul perintah untuk permintaan password. Sesuai dengan soal maka password yang diinputkan adalah `kcksit18`.

![inputpassword](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/c2f5bd55-cdc9-424d-ad00-a634bbcfd623)

- Kemudian buat script konfigurasi baru di `.bashrc` untuk `no10Lanjut.sh`

**no10Lanjut.sh**
```
# Salin file konfigurasi default ke lb_php
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

# Tambahkan konfigurasi load balancing ke dalam lb_php
cat <<EOL > /etc/nginx/sites-available/lb_php
upstream worker {
    server 192.242.1.1;
    server 192.242.1.2;
    server 192.242.1.3;
}

server {
    listen 80;
    server_name harkonen.it18.com www.harkonen.it18.com;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        proxy_pass http://worker;
        auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/supersecret/htpasswd;
    }
}
EOL

# Aktifkan konfigurasi baru
ln -sf /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/

# Hapus konfigurasi default jika ada
if [ -f /etc/nginx/sites-enabled/default ]; then
    rm /etc/nginx/sites-enabled/default
fi

# Restart layanan Nginx untuk menerapkan perubahan
service nginx restart
```

- Setelah tampilan seperti ini maka kita akan lanjut testing di Client.

![no10Lanjut](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/fcd43c20-809d-40ef-ac3a-b40b7567e4b9)

- Lakukan testing di Dmitri, jalankan `script.sh`, dan command `lynx harkonen.it18.com`.

- Pertama akan muncul Alert warning seperti ini.

![alert](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/4d802c8e-ab52-4f9c-9e09-2ac24ffddbaa)

- Setelahnya akan muncul tampilan ini dan masukkan username `secmart`.

![secmart](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/9becf833-3f8c-4b0f-b520-87a27beff109)

- Input password `kcksit18`

![password](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/0ed42852-f6b1-4da7-b48e-0a0fcdeaef5e)

- Apabila sudah seperti ini artinya sudah berhasil.

![no10](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/124652107/410b21c4-bd69-4694-acb4-563a14a5ec5c)

---

## **Soal 11**

Lalu buat untuk setiap request yang mengandung /dune akan di proxy passing menuju halaman https://www.dunemovie.com.au/. **hint: (proxy_pass)**

Tambahkan script berikut pada Load Balancer Stilgar supaya kita bisa terkoneksi dengan dune:

```bash
#!/bin/bash

cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

cat <<EOL > /etc/nginx/sites-available/lb_php
upstream worker {
    server 192.242.1.2;
    server 192.242.1.3;
    server 192.242.1.4;
}

server {
    listen 80;
    server_name harkonen.it18.com www.harkonen.it18.com;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    location / {
        proxy_pass http://worker;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }

    location /dune {
        rewrite ^/dune(.*)$ https://www.dunemovie.com.au\$1 break;
        proxy_pass https://www.dunemovie.com.au;
        proxy_set_header Host www.dunemovie.com.au;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOL

ln -sf /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/

# Hapus konfigurasi default jika ada
if [ -f /etc/nginx/sites-enabled/default ]; then
    rm /etc/nginx/sites-enabled/default
fi

service nginx restart
```

Setelah itu jalankan dengan command `lynx http://harkonen.it18.com/dune` pada client Dimitri.

### Hasil Soal 11

![modul3_11](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/840251e5-598c-4f4e-8e86-09f0730336fa)

---

## **Soal 12**

Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].1.37, [Prefix IP].1.67, [Prefix IP].2.203, dan [Prefix IP].2.207. **hint: (fixed in dulu clientnya)**

Tambahkan script berikut pada node Stilgar untuk memberikan akses izin ke IP yang ditentukan:

### stilgar.sh

```bash
#!/bin/bash

cp /etc/nginx/sites-available/default /etc/nginx/sites-available/lb_php

cat <<EOL > /etc/nginx/sites-available/lb_php
upstream worker {
    server 192.242.1.2;
    server 192.242.1.3;
    server 192.242.1.4;
}

server {
    listen 80;
    server_name harkonen.it18.com www.harkonen.it18.com;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    location / {
        allow 127.0.0.1;
        allow 192.242.1.37;
        allow 192.242.1.67;
        allow 192.242.2.203;
        allow 192.242.2.207;
        deny all;

        proxy_pass http://worker;
        # proxy_set_header Host \$host;
        # proxy_set_header X-Real-IP \$remote_addr;
        # proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        # proxy_set_header X-Forwarded-Proto \$scheme;
    }
}
EOL

ln -sf /etc/nginx/sites-available/lb_php /etc/nginx/sites-enabled/

if [ -f /etc/nginx/sites-enabled/default ]; then
    rm /etc/nginx/sites-enabled/default
fi

service nginx restart

```

Kemudian tentukan fixed client (client tetap yang memiliki akses). Di sini menggunakan Dimitri sebagai client tetapnya. Lalu tambahkan konfigurasi script berikut pada dhcp server Mohiam:

### mohiam.sh

```bash
#!/bin/bash

# Tambahkan konfigurasi DHCP untuk host Dimitri
cat <<EOL >> /etc/dhcp/dhcpd.conf

host Dimitri {
    hardware ethernet 22:e5:2c:02:e3:60;
    fixed-address 192.242.1.67;
    option host-name "Dimitri";
}
EOL

service isc-dhcp-server restart
```

- `22:e5:2c:02:e3:60` didapatkan dari node client Dimitri dengan menggunakan command `ip a` lalu periksa ip yang terhubung dengan DHCP Relay Arakis yaitu eth0. Catat ip ethernet tersebut.
- `192.242.1.67` didapatkan dengan menentukan ip `192.242.1.67` sebagai salah satu ip batasan yang sudah didtentukan di soal untuk ip client Dimitri. Setelah itu masukan `192.242.1.67` kedalam network configuration dengan command `nano /etc/network/interfaces`.

```
auto eth0
iface eth0 inet dhcp
hwaddress ether 22:e5:2c:02:e3:60
```

Script di atas memodifikasi `/etc/network/interfaces` dengan menambahkan `hwaddress ether 22:e5:2c:02:e3:60` di baris selanjutnya. Lebih lengkapnya berikut script bash untuk konfigurasi client pada Dimitri

### dimitri.sh

```bash
#!/bin/bash

# Tambahkan konfigurasi DHCP untuk host Dimitri
cat <<EOL >> /etc/dhcp/dhcpd.conf

host Dimitri {
    hardware ethernet 22:e5:2c:02:e3:60;
    fixed-address 192.242.1.67;
    option host-name "Dimitri";
}
EOL

# Restart layanan DHCP server
service isc-dhcp-server restart

```

Apabila dilakukan `lynx http://harkonen.it18.com/` pada client Dimitri, maka akan menampilkan web yang sesuai. Namun ketika dijalankan pada node lain seperti client Paul, maka tidak akan menampilkan hasil yang diinginkan karena tidak diberikan akses/

### Hasil Soal 12

#### Testing Selain Client Dimitri

![modul3_soal12b](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/4eb1a593-b529-464d-b606-dac48484af09)

#### Testing pada Client Dimitri

![modul3_soal12a](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/fa55697b-67e6-4d3d-b4de-b79696197109)

---

## **Soal 13**

Semua data yang diperlukan, diatur pada Chani dan harus dapat diakses oleh Leto, Duncan, dan Jessica.

Lakukan konfigurasi Database Server pada Chani sesuai pada script berikut ini:

### chani.sh
```bash
#!/bin/bash

apt-get update
apt-get install mariadb-server -y

service mysql restart

# Menjalankan MariaDB dan memasukkan perintah SQL
mysql <<EOF
CREATE USER 'kelompokit18'@'%' IDENTIFIED BY 'passwordit18';
CREATE USER 'kelompokit18'@'localhost' IDENTIFIED BY 'passwordit18';
CREATE DATABASE dbkelompokit18;
GRANT ALL PRIVILEGES ON *.* TO 'kelompokit18'@'%';
GRANT ALL PRIVILEGES ON *.* TO 'kelompokit18'@'localhost';
FLUSH PRIVILEGES;
EOF

# Tambahkan konfigurasi untuk mengizinkan koneksi jaringan
echo '[client-server]

# Import all .cnf files from configuration directory
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/

[mysqld]
skip-networking=0
skip-bind-address' >/etc/mysql/my.cnf

service mysql restart

# Verifikasi pembuatan pengguna dan basis data
mysql -u kelompokit18 -p'passwordit18' <<EOF
SHOW DATABASES;
quit
EOF
```

Selanjutnya Lakukan konfigurasi Laravel Worker pada setiap node (Leto, Duncan, Jessica) dengan melakukan instalasi package. Script ini bisa dimasukkan pada `.bashrc` dengan `nano /root/.bashrc` untuk mempercepat scripting.

```bash
apt-get update
apt-get install mariadb-client -y
apt-get install lynx -y
apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common gnupg2
curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg
sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'
apt-get update
apt-get install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl unzip wget -y
apt-get install nginx -y
wget https://getcomposer.org/download/2.0.13/composer.phar
chmod +x composer.phar
mv composer.phar /usr/bin/composer
```

Untuk memastikan bahwa database pada Chani juga dapat diakses melalui laravel worker, gunakan command berikut:

`mariadb --host=192.242.4.3 --port=3306 --user=kelompokit18 --password`

Sesuaikan IP pada `--host=192.242.4.3` dengan IP database server Chani.

### Hasil Soal 13

![modul3_13](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/87fe2135-2fa0-467d-9cdd-fae395e07d81)

---

## **Soal 14**
Leto, Duncan, dan Jessica memiliki atreides Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer.

Pertama-tama, lakukan instalasi berikut pada ketiga Laravel Worker

```bash
apt-get install php8.0-mbstring php8.0-xml php8.0-cli php8.0-common php8.0-intl php8.0-opcache php8.0-readline php8.0-mysql php8.0-fpm php8.0-curl unzip wget -y
wget https://getcomposer.org/download/2.0.13/composer.phar
chmod +x composer.phar
mv composer.phar /usr/bin/composer
```

Lakukan git clone dan konfigurasi pada masing-masing ketiga laravel worker, sesuaikan port listennya juga. Berikut pembagian port:

```
Leto    : 8001
Duncan  : 8002
Jessica : 8003
```

Masukkan semua konfigurasinya pada script berikut pada masing-masing worker laravel

### leto.sh
```bash
#!/bin/bash

echo "
nameserver 192.168.122.1
nameserver 192.242.4.3
" > /etc/resolv.conf

apt-get install git -y
cd /var/www && git clone https://github.com/martuafernando/laravel-praktikum-jarkom.git

cd /var/www/laravel-praktikum-jarkom && composer update
cd /var/www/laravel-praktikum-jarkom && composer install
cd /var/www/laravel-praktikum-jarkom && cp .env.example .env

echo '
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=192.242.4.3
DB_PORT=3306
DB_DATABASE=dbkelompokit18
DB_USERNAME=kelompokit18
DB_PASSWORD=passwordit18

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
' > /var/www/laravel-praktikum-jarkom/.env

cd /var/www/laravel-praktikum-jarkom && php artisan migrate:fresh
cd /var/www/laravel-praktikum-jarkom && php artisan db:seed --class=AiringsTableSeeder
cd /var/www/laravel-praktikum-jarkom && php artisan key:generate
cd /var/www/laravel-praktikum-jarkom && php artisan jwt:secret
cd /var/www/laravel-praktikum-jarkom && php artisan storage:link

chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage

echo '
server {
    listen 8001;

    root /var/www/laravel-praktikum-jarkom/public;

    index index.php index.html index.htm;
    server_name _;

    location / {
            try_files $uri $uri/ /index.php?$query_string;
    }

    # pass PHP scripts to FastCGI server
    location ~ \.php$ {
      include snippets/fastcgi-php.conf;
      fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
    }

    location ~ /\.ht {
            deny all;
    }

    error_log /var/log/nginx/praktikum-jarkom_error.log;
    access_log /var/log/nginx/praktikum-jarkom_access.log;
}
' > /etc/nginx/sites-available/praktikum-jarkom

ln -s /etc/nginx/sites-available/praktikum-jarkom /etc/nginx/sites-enabled/
chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage
service nginx restart
service php8.0-fpm start

#lynx localhost:8001
```

### duncan.sh
```bash
#!/bin/bash

echo "
nameserver 192.168.122.1
nameserver 192.242.4.3
" > /etc/resolv.conf

apt-get install git -y
cd /var/www && git clone https://github.com/martuafernando/laravel-praktikum-jarkom.git

cd /var/www/laravel-praktikum-jarkom && composer update
cd /var/www/laravel-praktikum-jarkom && composer install
cd /var/www/laravel-praktikum-jarkom && cp .env.example .env

echo '
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=192.242.4.3
DB_PORT=3306
DB_DATABASE=dbkelompokit18
DB_USERNAME=kelompokit18
DB_PASSWORD=passwordit18

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
' > /var/www/laravel-praktikum-jarkom/.env

cd /var/www/laravel-praktikum-jarkom && php artisan migrate:fresh
cd /var/www/laravel-praktikum-jarkom && php artisan db:seed --class=AiringsTableSeeder
cd /var/www/laravel-praktikum-jarkom && php artisan key:generate
cd /var/www/laravel-praktikum-jarkom && php artisan jwt:secret
cd /var/www/laravel-praktikum-jarkom && php artisan storage:link

chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage

echo '
server {
    listen 8002;

    root /var/www/laravel-praktikum-jarkom/public;

    index index.php index.html index.htm;
    server_name _;

    location / {
            try_files $uri $uri/ /index.php?$query_string;
    }

    # pass PHP scripts to FastCGI server
    location ~ \.php$ {
      include snippets/fastcgi-php.conf;
      fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
    }

    location ~ /\.ht {
            deny all;
    }

    error_log /var/log/nginx/praktikum-jarkom_error.log;
    access_log /var/log/nginx/praktikum-jarkom_access.log;
}
' > /etc/nginx/sites-available/praktikum-jarkom

ln -s /etc/nginx/sites-available/praktikum-jarkom /etc/nginx/sites-enabled/
chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage
service nginx restart
service php8.0-fpm start

#lynx localhost:8002
```

### jessica.sh
```bash
#!/bin/bash

echo "
nameserver 192.168.122.1
nameserver 192.242.4.3
" > /etc/resolv.conf

apt-get install git -y
cd /var/www && git clone https://github.com/martuafernando/laravel-praktikum-jarkom.git

cd /var/www/laravel-praktikum-jarkom && composer update
cd /var/www/laravel-praktikum-jarkom && composer install
cd /var/www/laravel-praktikum-jarkom && cp .env.example .env

echo '
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=192.242.4.3
DB_PORT=3306
DB_DATABASE=dbkelompokit18
DB_USERNAME=kelompokit18
DB_PASSWORD=passwordit18

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
' > /var/www/laravel-praktikum-jarkom/.env

cd /var/www/laravel-praktikum-jarkom && php artisan migrate:fresh
cd /var/www/laravel-praktikum-jarkom && php artisan db:seed --class=AiringsTableSeeder
cd /var/www/laravel-praktikum-jarkom && php artisan key:generate
cd /var/www/laravel-praktikum-jarkom && php artisan jwt:secret
cd /var/www/laravel-praktikum-jarkom && php artisan storage:link

chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage

echo '
server {
    listen 8003;

    root /var/www/laravel-praktikum-jarkom/public;

    index index.php index.html index.htm;
    server_name _;

    location / {
            try_files $uri $uri/ /index.php?$query_string;
    }

    # pass PHP scripts to FastCGI server
    location ~ \.php$ {
      include snippets/fastcgi-php.conf;
      fastcgi_pass unix:/var/run/php/php8.0-fpm.sock;
    }

    location ~ /\.ht {
            deny all;
    }

    error_log /var/log/nginx/praktikum-jarkom_error.log;
    access_log /var/log/nginx/praktikum-jarkom_access.log;
}
' > /etc/nginx/sites-available/praktikum-jarkom

ln -s /etc/nginx/sites-available/praktikum-jarkom /etc/nginx/sites-enabled/
chown -R www-data.www-data /var/www/laravel-praktikum-jarkom/storage
service nginx restart
service php8.0-fpm start

#lynx localhost:8003
```

Setelah script selesai dan dijalankan pada masing-masing worker laravel, selanjutnya lakukan test pada masing-masing worker laravel menggunakan `lynx localhost:[port]` (port menyesuaikan worker laravel)

### Hasil Soal 14

![modul3_soal14](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/ae7a8264-58ea-4a37-9f9b-e06976f34366)

---

## **Soal 15**

atreides Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada peta. 

- POST /auth/register

Buat file `register.json` pada client yang berisi 
```
{
  "username": "kelompokit18",
  "password": "passwordit18"
}
```

Kemudian jalankan command berikut pada client juga

```
ab -n 100 -c 10 -p register.json -T application/json http://192.242.2.2:8001/api/auth/register
```

### Hasil Soal 15

![modul3_soal15](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/e4f63aeb-8faa-41d1-8c66-4c6978d1f6d2)

---

## **Soal 16**

- POST /auth/login

Buat file `login.json` pada client yang berisi 
```
{
  "username": "kelompokit18",
  "password": "passwordit18"
}
```

Kemudian jalankan command berikut pada client juga

```
ab -n 100 -c 10 -p login.json -T application/json http://192.242.2.2:8001/api/auth/login
```

### Hasil Soal 16

![modul3_soal16](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/b14b77b0-c852-4e62-8e90-332b5906fa92)

---

## **Soal 17**

GET /me

Kemudian jalankan command berikut
```
curl -X POST -H "Content-Type: application/json" -d @regis.json http://192.242.2.2:8001/api/auth/login > login_token.txt
```

Jalankan testing berikut
```
ab -n 100 -c 10 -H "Authorization: Bearer $token" http://192.242.2.2:8001/api/me
```

### Hasil Soal 17

![modul3_soal17](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/9e22a42e-e6ef-4df9-9eee-f584cb31dbf0)

---

## **Soal 18**

Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur atreides Channel maka implementasikan Proxy Bind pada Stilgar untuk mengaitkan IP dari Leto, Duncan, dan Jessica.

Lakukan konfigurasi Load Balancer pada STilgar dengan script berikut:

### stilgar.sh

```bash
echo 'upstream worker {
    server 192.242.2.2:8001;
    server 192.242.2.3:8002;
    server 192.242.2.4:8003;
}

server {
    listen 80;
    server_name atreides.it18.com www.atreides.it18.com;

    location / {
        proxy_pass http://worker;
    }
}
' > /etc/nginx/sites-available/laravel-worker

service nginx restart
```

Pastikan dan cek kembali pada Domain Name Server Irulam, atreides.it18.com mengarah pada IP Load Balancer yaitu `192.242.4.2`

Lakukan testing menggunakan coommand berikut:
```
ab  -n 100 -c 10 -p register.json -T application/json http://atreides.it18.com/api/auth/register
```

### Hasil Soal 18

![mosul3_soal18ok](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/c9dace54-fec4-47a5-a906-ccbbf321888b)

- Leto
![modul3_soal18leto](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/9fbd8f24-88c1-4022-a1b8-bfebc70333e2)

- Duncan
![modul3_soal18duncan](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/aea4de83-eee7-4f45-975a-4b763cebd30a)

- Jessica
![modul3_soal18jessica](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/b9cc7cb4-c3d8-4915-a9d6-a382a359aff2)

---

## **Soal 19**

Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Leto, Duncan, dan Jessica. Untuk testing kinerja naikkan 
- pm.max_children
- pm.start_servers
- pm.min_spare_servers
- pm.max_spare_servers
sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada PDF.

Buat dan jalankan 3 script testing berikut secara bergantian pada masing-masing worker laravel (Leto, Duncan, Jessica):

### testing1.sh
```bash
#!/bin/bash

echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 10
pm.start_servers = 5
pm.min_spare_servers = 3
pm.max_spare_servers = 8' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart
```

### testing2.sh
```bash
#!/bin/bash

echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 20
pm.start_servers = 8
pm.min_spare_servers = 5
pm.max_spare_servers = 12' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart
```

### testing3.sh
```bash
#!/bin/bash

echo '[www]
user = www-data
group = www-data
listen = /run/php/php8.0-fpm.sock
listen.owner = www-data
listen.group = www-data
php_admin_value[disable_functions] = exec,passthru,shell_exec,system
php_admin_flag[allow_url_fopen] = off

; Choose how the process manager will control the number of child processes.

pm = dynamic
pm.max_children = 40
pm.start_servers = 10
pm.min_spare_servers = 8
pm.max_spare_servers = 15' > /etc/php/8.0/fpm/pool.d/www.conf

service php8.0-fpm restart
```

Masukkan semua testing ini pada semua worker laravel dan jalankan, jika testing 1 selesai, maka lanjutkan testing 2, dan testing 3 untuk melihat perbedaannya.

Gunakan command ini untuk melakukan pengujian di node client
```
ab -n 100 -c 10 -p login.json -T application/json http://atreides.it18.com/api/auth/login
```

### Hasil Soal 19
- testing1.sh

![modul3_soal19test1](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/4d058750-ae71-4b9e-8e7e-9942080fa775)

- testing2.sh

![modul3_soal19test2](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/89839e98-5326-4560-a021-344a0427df6e)

- testing3.sh

![modul3_soal19test3](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/e0c2de0e-06b0-4968-bd84-c6e57de2e2bb)

---

## **Soal 20**

Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Stilgar. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second.

Tambahkan konfigurasi seperti berikut pada Stilgar:

### stilgar20.sh
```bash
echo 'upstream worker {
    least_conn; # Algoritma Least-Connection
    server 192.242.2.2:8001;
    server 192.242.2.3:8002;
    server 192.242.2.4:8003;
}

server {
    listen 80;
    server_name atreides.it18.com www.atreides.it18.com;

    location / {
        proxy_pass http://worker;
    }
}
' > /etc/nginx/sites-available/laravel-worker

service nginx restart
```

Setelah itu jalankan pada Stilgar, lalu testing menggunakan command berikut pada node client:

```
ab -n 100 -c 10 -p login.json -T application/json http://atreides.it18.com/api/auth/login
```

---

## **Kendala**
- Terkadang bash `<script>.sh` sering error, awal-awal pengerjaan memang masih bisa, tetapi lama kelamaan terjadi error. Jadi harus dijalankan di hidden root. Bisa dibuat dan dijalankan dengan `nano /root/.bashrc` dan `source /root/.bashrc`

---

## **Notes**

Apabila tidak ingin script menghilang masuk ke dalam root terlebih dahulu dengan `cd`. 'semoga'

Untuk nomor 0 - 10, disetiap awal mulai konfigurasi, diwajibkan untuk menjalankan `script.sh` mengantisipasi error pada command selanjutnya, isi `script.sh` bisa disesuaikan dengan bagian-bagian pengerjaan.

Link Peta IT18 bisa dilihat [di sini! ](https://docs.google.com/document/d/1rr2sojHuQ5QvsLBgPMSe74EFLzLnCBEr8_r42Z7H5xA/edit?usp=sharing)

Untuk nomor 11-20 karena dikerjakan terpisah dan router nya memiliki address akhir 0, menggunakan IP Address yang berbeda, dengan list sebagai berikut :

**Setup**

Irulam (DNS Server) - 192.242.3.2

Stilgar (Load Balancer) - 192.242.4.2

Mohiam (DHCP Server) - 192.242.3.3

Chani (Database Server) - 192.242.4.3

**Laravel Worker**

Leto (Laravel Worker) - 192.242.2.2

Duncan (Laravel Worker) - 192.242.2.3

Jessica (Laravel Worker) - 192.242.2.4

**PHP Worker**

Vladimir (PHP Worker) - 192.242.1.2

Rabban (PHP Worker) - 192.242.1.3

Feyd (PHP Worker) - 192.242.1.4


