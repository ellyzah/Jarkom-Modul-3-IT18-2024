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
Lalu buat untuk setiap request yang mengandung /dune akan di proxy passing menuju halaman https://www.dunemovie.com.au/. (11) **hint: (proxy_pass)**

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
Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].1.37, [Prefix IP].1.67, [Prefix IP].2.203, dan [Prefix IP].2.207. (12) **hint: (fixed in dulu clientnya)**

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

### mohiam-12.sh
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


#### Testing pada Client Dimitri
                                                                                                                                                                                                                                                                                                                                                                                                                                



## **Soal 13**


## **Soal 14**


## **Soal 16**


## **Soal 17**


## **Soal 18**


## **Soal 19**


## **Soal 20**
