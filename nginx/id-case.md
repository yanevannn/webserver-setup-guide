# **Studi Kasus: Menyiapkan Web Server Nginx dengan Dua Domain (Port Berbeda)**

**Tujuan:** Mengkonfigurasi Nginx untuk menghosting dua website dengan domain berbeda:

*   `domain1.com` di port default 80 (HTTP).
*   `domain2.com` di port 8001.

**Prasyarat:**

*   Server dengan sistem operasi berbasis Debian/Ubuntu (perintah `apt` digunakan).
*   Akses `sudo` atau *root*.
*   Pemahaman dasar perintah terminal.
*   **Nginx sudah terinstal.**
*   Nama domain yang sudah terdaftar dan diarahkan ke IP server (untuk akses publik). Untuk pengujian lokal, bisa menggunakan `localhost` ( 127.0.0.1 ) atau IP server langsung.
*   Firewall yang dikonfigurasi (jika ada).

**Langkah-Langkah:**

1.  **Buat Direktori Website:**

    Buat direktori untuk file website di `/var/www/`:

    ```bash
    sudo mkdir -p /var/www/domain1.com/public_html
    sudo mkdir -p /var/www/domain2.com/public_html
    ```
<br>

2.  **Ubah Kepemilikan dan Izin Direktori:**

    Ubah kepemilikan ke user dan group `www-data` dan atur izin agar Nginx dapat mengakses file:

    ```bash
    sudo chown -R www-data:www-data /var/www/domain1.com/public_html
    sudo chown -R www-data:www-data /var/www/domain2.com/public_html
    sudo chmod 775 /var/www/domain1.com/public_html
    sudo chmod 775 /var/www/domain2.com/public_html
    ```
<br>

3.  **Buat File `index.html`:**

    Buat file `index.html` untuk setiap domain.

    *   **domain1.com:**

        ```bash
        sudo nano /var/www/domain1.com/public_html/index.html
        ```

        Isi dengan:

        ```html
        <!DOCTYPE html>
        <html>
        <head>
            <title>Selamat Datang di domain1.com</title>
        </head>
        <body>
            <h1>Selamat Datang di domain1.com!</h1>
            <p>Web ini berjalan di port 80 (HTTP).</p>
        </body>
        </html>
        ```

    *   **domain2.com:**

        ```bash
        sudo nano /var/www/domain2.com/public_html/index.html
        ```

        Isi dengan:

        ```html
        <!DOCTYPE html>
        <html>
        <head>
            <title>Selamat Datang di domain2.com</title>
        </head>
        <body>
            <h1>Selamat Datang di domain2.com!</h1>
            <p>Web ini berjalan di port 8001.</p>
        </body>
        </html>
        ```
<br>

4.  **Konfigurasi Virtual Host Nginx:**

    Buat file konfigurasi virtual host di `/etc/nginx/sites-available/`.

    *   **domain1.com (Port 80):**

        ```bash
        sudo nano /etc/nginx/sites-available/domain1.com
        ```

        Isi dengan:

        ```nginx
        server {
            listen 80;
            listen [::]:80; # Untuk IPv6
            server_name 127.0.0.1;
            root /var/www/domain1.com/public_html;
            index index.html index.htm;

            location / {
                try_files $uri $uri/ =404;
            }
        }
        ```

    *   **domain2.com (Port 8001):**

        ```bash
        sudo nano /etc/nginx/sites-available/domain2.com
        ```

        Isi dengan:

        ```nginx
        server {
            listen 8001;
            listen [::]:8001; # Untuk IPv6
            server_name 127.0.0.1;
            root /var/www/domain2.com/public_html;
            index index.html index.htm;

            location / {
                try_files $uri $uri/ =404;
            }
        }
        ```
<br>

5.  **Aktifkan Virtual Host:**

    Buat *symlink* ke direktori `sites-enabled/`:

    ```bash
    sudo ln -s /etc/nginx/sites-available/domain1.com /etc/nginx/sites-enabled/
    sudo ln -s /etc/nginx/sites-available/domain2.com /etc/nginx/sites-enabled/
    ```
<br>

6.  **Uji Konfigurasi Nginx:**

    Periksa sintaks konfigurasi:

    ```bash
    sudo nginx -t
    ```

    **Penting:** Perhatikan output dari perintah ini. Pastikan tidak ada pesan _error_. Jika ada, perbaiki konfigurasi sebelum melanjutkan.

<br>

7.  **Restart Nginx:**

    Terapkan perubahan konfigurasi:

    ```bash
    sudo systemctl restart nginx
    ```
<br>

8. **Verifikasi Hasil:**

    Apabila menggunakan Local IP (localhost), pengecekan hasil dapat dilakukan sebagai berikut:

    *   Akses `http://127.0.0.1` di browser.
    *   Akses `http://127.0.0.1:8001` di browser.

    Apabila menggunakan domain, pengecekan hasil dapat dilakukan sebagai berikut:

    *   Akses `http://domain1.com` di browser.
    *   Akses `http://domain2.com:8001` di browser.

    Apabila menggunakan IP VPS, pengecekan hasil dapat dilakukan sebagai berikut:

    *   Akses `http://IP-VPS` di browser.
    *   Akses `http://IP-VPS:8001` di browser.

    Halaman website akan tampil sesuai dengan file dan konfigurasinya.
    
<br>

9. **Konfigurasi Firewall (Jika Diperlukan):**

    Buka port 8001 di firewall (contoh dengan `ufw`):

    ```bash
    sudo ufw allow 8001/tcp
    sudo ufw reload
    ```



