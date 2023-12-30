# Instalasi dan Konfigurasi Mod_Evasive

## 1. Instalasi
Pertama-tama, kita akan install dan mengaktivasi repositori Epel

# wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
# rpm -ivh epel-release-latest-7.noarch.rpm

Kemudian, kita akan install modul mod_evasive

# yum install mod_evasive -y

--------------------------------------------------------------------------------------

## 2. Verifikasi kelengkapan instalasi
Kita akan verifikasi dengan membuka file konfigurasi modul

# cat /etc/httpd/conf.d/mod_evasive.conf

Pastikan line berikut ada di bagian paling atas dalam file konfigurasi modul tadi

LoadModule evasive20_module modules/mod_evasive24.so

Restart service httpd dengan command berikut:

# httpd -M | grep -Ei '(evasive)'

--------------------------------------------------------------------------------------

## 3. konfigurasi
Konfigurasi mod_evasive di file:

# nano  /etc/httpd/conf.d/mod_evasive.conf
