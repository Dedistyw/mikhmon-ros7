# Mikhmon ROS7 — Mikrotik Hotspot Monitor (Web Server & Docker Ready)

Mikhmon ROS7 adalah aplikasi berbasis PHP untuk manajemen dan monitoring hotspot MikroTik RouterOS 7. Aplikasi ini ringan, portable, dan tidak membutuhkan database eksternal karena konfigurasi disimpan pada file.

Repository ini disusun agar instalasi dapat dilakukan dengan dua metode:
- Dipasang langsung pada web server (Apache / Nginx)
- Dipasang menggunakan Docker + Docker Compose

---

## Daftar Isi
- [Fitur Utama](#✨-fitur-utama)
- [Struktur Direktori](#📂-struktur-direktori)
- [Persyaratan](#📌-persyaratan)
- [Instalasi tanpa Docker (Web Server)](#🟢-instalasi-tanpa-docker-web-server)
  - [1. Install Web Server & PHP](#1-install-web-server--php)
  - [2. Clone Repository](#2-clone-repository)
  - [3. Set Permission](#3-set-permission)
  - [4. Konfigurasi Nginx (Contoh)](#4-konfigurasi-nginx-contoh)
  - [5. Akses Aplikasi](#5-akses-aplikasi)
- [Instalasi dengan Docker & Docker Compose](#🟡-instalasi-dengan-docker--docker-compose)
- [Update / Upgrade Aplikasi](#🔄-update--upgrade-aplikasi)
- [Catatan Keamanan](#⚠️-catatan-keamanan)
- [Kontribusi](#🤝-kontribusi)
- [Lisensi](#📝-lisensi)

---

✨ Fitur Utama
- Manajemen user hotspot
- Monitoring session & traffic
- Generate voucher
- Export & report
- Ringan (tanpa database eksternal)
- Support RouterOS 7
- Dapat dijalankan di server biasa maupun Docker

---

📂 Struktur Direktori
```
mikhmon-ros7/
├── css/
├── js/
├── img/
├── include/
├── hotspot/
├── report/
├── settings/
├── voucher/
├── index.php
└── (file aplikasi lainnya)
```

---

📌 Persyaratan
- PHP 7.4+ (atau versi PHP yang kompatibel)
- Ekstensi PHP: curl, zip, mbstring
- Nginx atau Apache (jika tidak menggunakan Docker)
- Docker & Docker Compose (opsional, untuk metode Docker)

---

🟢 INSTALASI TANPA DOCKER (WEB SERVER)

Metode ini cocok untuk:
- VPS / Dedicated Server
- Hosting panel (CWP, CyberPanel, Webmin)
- Instalasi server manual

1. Install Web Server & PHP

- Contoh untuk Ubuntu / Debian:
```bash
sudo apt update
sudo apt install -y nginx php-fpm php-cli php-curl php-zip php-mbstring
```

- Contoh untuk CentOS / AlmaLinux / Rocky:
```bash
sudo yum install -y epel-release
sudo yum install -y nginx php php-fpm php-cli php-curl php-zip php-mbstring
```

2. Clone Repository
```bash
cd /var/www/html
git clone https://github.com/Dedistyw/mikhmon-ros7.git
cd mikhmon-ros7
```

3. Set Permission
Sesuaikan user service (`www-data`, `nginx`, atau `apache`) sesuai distro Anda.
```bash
sudo chown -R www-data:www-data /var/www/html/mikhmon-ros7
sudo find /var/www/html/mikhmon-ros7 -type d -exec chmod 755 {} \;
sudo find /var/www/html/mikhmon-ros7 -type f -exec chmod 644 {} \;
```

4. Konfigurasi Nginx (Contoh)
Buat file konfigurasi, mis. `/etc/nginx/sites-available/mikhmon` lalu aktifkan symlink ke `sites-enabled`.
```nginx
server {
    listen 80;
    server_name _;

    root /var/www/html/mikhmon-ros7;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        # Sesuaikan socket PHP-FPM path sesuai distro:
        # Debian/Ubuntu biasanya: unix:/run/php/php7.4-fpm.sock atau php8.1-fpm.sock
        # CentOS/AlmaLinux biasanya: unix:/run/php-fpm/www.sock
        fastcgi_pass unix:/run/php/php-fpm.sock;
        fastcgi_index index.php;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Reload/Restart service:
```bash
sudo systemctl restart php-fpm
sudo systemctl restart nginx
```
Catatan: Nama service PHP-FPM bisa berbeda (php7.4-fpm, php8.1-fpm, php-fpm). Gunakan `systemctl status` untuk mengecek nama yang benar.

5. Akses Aplikasi
Buka browser:
```
http://IP-SERVER/
```

---

🟡 INSTALASI DENGAN DOCKER & DOCKER COMPOSE

Metode ini cocok untuk:
- CasaOS, Portainer
- VPS ringan
- Install portable / cepat deploy

1. Pastikan Docker & Compose Terpasang
```bash
docker --version
docker compose version
```

2. Clone Repository
```bash
git clone https://github.com/Dedistyw/mikhmon-ros7.git
cd mikhmon-ros7
```

3. Jalankan Aplikasi
```bash
docker compose up -d
```
Default berjalan di port 8080 (pastikan tidak bentrok dengan layanan lain).

4. Akses Aplikasi
Buka browser:
```
http://IP-SERVER:8080
```

---

🔄 UPDATE / UPGRADE APLIKASI

Jika Anda menginstal dari Git:
```bash
cd /var/www/html/mikhmon-ros7
git pull origin main
```

Jika menggunakan Docker:
```bash
cd /path/to/mikhmon-ros7
git pull origin main
docker compose pull
docker compose up -d --remove-orphans
```

---

⚠️ CATATAN KEAMANAN
- Jangan commit file yang berisi credential MikroTik ke Git.
- Batasi akses panel hanya dari IP admin atau gunakan firewall.
- Backup konfigurasi dan file sebelum melakukan update.
- Jika aplikasi dipublikasikan ke internet, gunakan reverse proxy dengan SSL (Let's Encrypt) dan proteksi tambahan (IP whitelist, HTTP auth).

---

🤝 KONTRIBUSI
Pull request & laporan issue sangat dipersilakan. Untuk kontribusi:
1. Fork repo
2. Buat branch fitur/bugfix
3. Submit PR dengan deskripsi perubahan dan langkah reproduksi jika bug

---

📝 LISENSI
Proyek ini mengikuti lisensi sesuai lisensi asli Mikhmon by Laksamadi-guko. Periksa file lisensi di repository untuk detail lebih lanjut.

---
