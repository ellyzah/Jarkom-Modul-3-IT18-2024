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

0. [Soal 0](#Soal-0)
1. [Soal 1](#Soal-1)
2. [Soal 2](#Soal-2)
3. [Soal 3](#Soal-3)
4. [Soal 4](#Soal-4)
5. [Soal 5](#Soal-5)
6. [Soal 6](#Soal-6)
7. [Soal 7](#Soal-7)
8. [Soal 8](#Soal-8)
9. [Soal 9](#Soal-9)
10. [Soal 10](#Soal-10)
11. [Soal 11](#Soal-11)
12. [Soal 12](#Soal-12)
13. [Soal 13](#Soal-13)
14. [Soal 14](#Soal-14)
15. [Soal 15](#Soal-15)
16. [Soal 16](#Soal-16)
17. [Soal 17](#Soal-17)
18. [Soal 18](#Soal-18)
19. [Soal 19](#Soal-19)
20. [Soal 20](#Soal-20)

## **Soal 0**

## **Soal 1**

## **Soal 2**

## **Soal 3**

## **Soal 4**

## **Soal 5**

## **Soal 6**

## **Soal 7**

## **Soal 8**

## **Soal 9**

## **Soal 10**

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

![alt text](modul3_soal12b.png)

#### Testing pada Client Dimitri

![alt text](modul3_soal12a.png)

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

### Hasil Soal 12

![alt text](modul3_13.png)

---

## **Soal 14**

## **Soal 16**

## **Soal 17**

## **Soal 18**

## **Soal 19**

## **Soal 20**
