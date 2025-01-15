# Panduan Setup Nginx

Berikut adalah langkah-langkah untuk menginstall dan mengkonfigurasi Nginx pada server.

## 1. Update dan Upgrade Package
Sebelum memulai instalasi, pastikan semua package di sistem sudah diperbarui.

```bash
sudo apt update && sudo apt upgrade -y
```

## 2. Instal Nginx
Setelah sistem diperbarui, instal Nginx dengan perintah berikut:

```bash
sudo apt install nginx -y
```
Tunggu hingga proses instalasi selesai.

## 3. Verifikasi Instalasi
Untuk memastikan Nginx berhasil diinstal, verifikasi versinya dengan perintah:
```bash
sudo nginx -v
```

### 4. Cek Status Nginx
Setelah instalasi berhasil, pastikan Nginx berjalan dengan baik dengan memeriksa statusnya:

```bash
sudo systemctl status nginx
```
Jika Nginx berjalan, Anda akan melihat status seperti ___active (running)___. Apabila Nginx belum berjalan, gunakan perintah berikut untuk memulai layanan:
```bash
sudo systemctl start nginx
```
Untuk me-restart Nginx setelah perubahan konfigurasi:

```bash
sudo systemctl restart nginx
```

Untuk menghentikan Nginx:

```bash
sudo systemctl stop nginx
```

## 5. Cek Localhost

Setelah Nginx berjalan, akses *localhost* (atau 127.0.0.1) melalui *web browser*. Halaman *default* Nginx akan ditampilkan jika instalasi berhasil.

Gunakan `curl` dari terminal:

```bash
curl http://localhost
```

Kode HTML halaman *default* Nginx akan ditampilkan jika berfungsi.


## 6. Lokasi Folder Konfigurasi dan Kegunaannya

*   **/etc/nginx/**: Direktori utama konfigurasi.
    *   **nginx.conf:** Konfigurasi utama (jumlah *worker processes*, *event handling*, *logging*). Contoh pengaturan:
        *   `user`: User Nginx.
        *   `worker_processes`: Jumlah proses worker.
        *   `events { worker_connections }`: Konfigurasi event.
        *   `http { ... }`: Blok konfigurasi HTTP.
    *   **conf.d/**: File konfigurasi tambahan (di-*include* oleh `nginx.conf`).
    *   **sites-available/**: File konfigurasi *virtual host* (belum aktif).
    *   **sites-enabled/**: *Symlink* ke *virtual host* (aktif).
    *   **snippets/**: Potongan konfigurasi (misal, SSL).

*   **/var/log/nginx/**: Direktori *log*.
    *   **access.log:** Catatan permintaan (analisis trafik).
    *   **error.log:** Catatan kesalahan (*troubleshooting*).

*   **/usr/share/nginx/html/**: Direktori *default document root* (berisi `index.html`).
sc

## 7. Cara Menggunakan Nginx (Konfigurasi Virtual Host)

Nginx menghosting beberapa situs dengan *virtual host* (*server block*). Langkah-langkah:

1.  Buat file konfigurasi *virtual host* di `sites-available/`:

    ```bash
    sudo nano /etc/nginx/sites-available/example.com
    ```

    Contoh isi ( `/etc/nginx/sites-available/example.com` ):

    ```nginx
    server {
        listen 80; # Untuk IPv4
        listen [::]:80; # Untuk IPv6
        server_name 127.0.0.1

        root /var/www/example.com; #directory

        index index.html index.htm;

        location / {
            try_files $uri $uri/ =404;
        }
    }
    ```

    Penjelasan Konfigurasi:

    *   `listen 80;` dan `listen [::]:80;`: Port 80 (HTTP) untuk IPv4 dan IPv6.
    *   `server_name`: Nama domain/subdomain atau ip public VPS.
    *   `root`: Direktori file website.
    *   `index`: File index.
    *   `location /`: Blok konfigurasi request.
    *   `try_files $uri $uri/ =404;`: Cari file/direktori, error 404 jika tidak ada.

2.  Buat direktori *root*:

    ```bash
    sudo mkdir -p /var/www/example.com
    ```

    Pastikan *user* Nginx punya izin membaca.

3.  Buat `index.html`:

    ```bash
    sudo nano /var/www/[example.com/index.html](https://example.com/index.html)
    ```

    Contoh isi `index.html`:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title>Selamat Datang di example.com</title>
    </head>
    <body>
        <h1>Ini adalah halaman website Anda!</h1>
    </body>
    </html>
    ```

4.  Aktifkan *virtual host* (buat *symlink*):

    ```bash
    sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
    ```

5.  Uji konfigurasi:

    ```bash
    sudo nginx -t
    ```

    Pastikan tidak ada error.

6.  Restart Nginx:

    ```bash
    sudo systemctl restart nginx
    ```

## Catatan Lainnya
**Studi Kasus Konfigurasi Web Server :** [Menyiapkan Web Server Nginx dengan Dua Domain (Port Berbeda)](./id-case.md)


## Referensi 

**Panduan Instalasi Nginx Open Source:** [Klik Disini](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/)











