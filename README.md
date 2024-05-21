# **LAPORAN RESMI PRAKTIKUM KOMUNIKASI DATA & JARINGAN KOMPUTER MODUL 3**

Berikut adalah Laporan Resmi Praktikum Komunikasi Data & Jaringan Komputer Modul 3 oleh Kelompok IT18.

| Nama                 | NRP        |
| -------------------- | ---------- |
| Iki Adfi Nur Mohamad | 5027221033 |
| Siti Nur Ellyzah     | 5027221014 |

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

- [Notes](#Notes)

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

![modul3_soal12b](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/4eb1a593-b529-464d-b606-dac48484af09)

#### Testing pada Client Dimitri

![modul3_soal12a](https://github.com/ellyzah/Jarkom-Modul-3-IT18-2024/assets/120791817/fa55697b-67e6-4d3d-b4de-b79696197109)

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

## **Notes**

Link Peta IT18 bisa dilihat [di sini! ](https://docs.google.com/document/d/1FOOV__UZe9O7gNeBkyqqwQiV6qZvSQbuiZW1eFQZbzI/edit)

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


