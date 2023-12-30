# Modsecurity

## 1. Instalasi Modsecurity

yum install mod_security

Setelah kita install, kita restart web server

systemctl restart httpd

------------------------------------------------------------------------------------------

## 2. Letak File Konfigurasi Modsecurity
### File utama
File konfigurasi utama untuk modul apache mod_security

# /etc/httpd/conf.d/mod_security.conf

### Direktori Konfigurasi
Berisi semua file konfigurasi lain untuk modul apache mod_security

# /etc/httpd/modsecurity.d/

### Konfigurasi Core Rule Set (CRS)
File konfigurasi untuk CRS

# /etc/httpd/modsecurity.d/crs-setup.conf

### Debugging ModSecurity Rules
Untuk debug rules mod_security dan masalah lainnya

# /var/log/httpd/modsec_debug.log

### File Log ModSecurity
Semua permintaan yang memicu peristiwa ModSecurity(seperti terdeteksi) atau kesalahan server dicatat("RelevantOnly") masuk ke file ini

# /var/log/httpd/modsec_audit.log

------------------------------------------------------------------------------------------

## 3. Konfigurasi ModSecurity
Pertama-tama, kita akan mendapatkan Core Rule Set(CRS) terlebih dahulu

# mkdir /etc/httpd/crs
# cd /etc/httpd/crs
# git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git


Kemudian masuk ke dalam direktori OWASP CRS

# cd /etc/httpd/crs/owasp-modsecurity-crs/


Pada direktori CRS OWASP, ada file crs-setup.conf.example
Kita akan salin isinya ke file baru yg bernama crs-setup.conf

# cp crs-setup.conf.example crs-setup.conf


Selanjutnya, kita akan konfigurasi Apache untuk menggunakan konfigurasi file tersebut dengan mengedit file konfigurasi utama ModSecurity

# nano /etc/httpd/conf.d/mod_security.conf

Sesuaikan konfigurasi seperti berikut:

# ModSecurity Core Rules Set configuration
	#IncludeOptional modsecurity.d/*.conf
	#IncludeOptional modsecurity.d/activated_rules/*.conf
	 IncludeOptional /etc/httpd/crs/owasp-modsecurity-crs/crs-setup.conf
	 IncludeOptional /etc/httpd/crs/owasp-modsecurity-crs/rules/*.conf

Restart Web Server

# systemctl restart httpd

Sesuaikan file konfigurasi sendiri dalam direktori conf.d

# nano /etc/httpd/conf.d/mod_security.conf

Sesuaikan script berikut :

SecResponseBodyAccess On

Restart web server:

# /etc/init.d/httpd restart


------------------------------------------------------------------------------------------

## 4. ModSecurity Testing
Testing akses shell command pada server:

# curl 192.168.20.220/index.php?exec=/bin/bash

Testing SQL Injection dengan SQLMap:

# python sqlmap.py --url http://192.168.20.220/read.php?id=26 --dbs

------------------------------------------------------------------------------------------


## 5. Logging ModSecurity
Melihat histori pada modsecurity

# tail -f /var/log/httpd/modsec_audit.log

------------------------------------------------------------------------------------------

## 6. Whitelist IP Address menggunakan ModSecurity
Pembatasan ijin akses terhadap sebuah halaman tertentu, perlu adanya aturan tertentu.
Misalkan halaman login administrator yang hanya dapat diakses oleh IP Address tertentu. Untuk itu perlu dibuat sebuah file yang mengimplementasikan aturan tersebut.

Membuat file adminPage.conf:

# touch /etc/httpd/conf.d/adminPage.conf
# nano /etc/httpd/conf.d/adminPage.conf


Masukkan konfigurasi sebagai berikut:

<IfModule mod_rewrite.c>
        RewriteEngine on
        RewriteCond %{REQUEST_URI} ^(.*)admin(.*)$
        RewriteCond %{REMOTE_ADDR} !^192\.168\.1\.65$ ## IP Addr
        RewriteRule .* / [R=302,L]
        <LocationMatch "/administrator">
                SecRuleEngine   DetectionOnly
        </LocationMatch>
</IfModule>













Sumber : https://csirt.baliprov.go.id/tutorial-instalasi-modsecurity-pada-centos-7/
