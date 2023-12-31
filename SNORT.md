# Install & Configuration Snort On Centos

## Menyiapkan Server

Pertama-tama, kita perlu install library yg dibutuhkan sebelum install snort
```
sudo yum install -y gcc flex bison zlib libcap pcre libdnet tcpdump
```
Kemudian kita install juga library tambahan untuk OS CentOS
```
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum install -y libnghttp2
```
-------------------------------------------------------------------------------------

## Install from Source

Install terlebih dahulu prerequisites snort
```
sudo yum install -y zlib-devel libpcap-devel pcre-devel libdnet-devel openssl-devel libnghttp2-devel luajit-devel
```

Buat directory snort dari root, lalu pindah ke directory tersebut
```
mkdir ~/snort_src && cd ~/snort_src
```

Install daq snort dengan wget
```
wget https://www.snort.org/downloads/snort/daq-2.0.7.tar.gz
```

Unzip file daq tersebut lalu pindah ke directory daq
```
tar -xvzf daq-2.0.7.tar.gz
cd daq-2.0.7
```

Jalankan configure untuk daq
```
./configure && make && sudo make install
```

Kembali ke directory snort
```
cd ~/snort_src
```

Lalu, install snort dengan wget
```
wget https://www.snort.org/downloads/snort/snort-2.9.20.tar.gz
```

Unzip file snort tersebut lalu pindah ke directory download snort
```
tar -xvzf snort-2.9.20.tar.gz
cd snort-2.9.20
```

Export path daq ke direktori snort berikut
```
export PATH=$PATH:/usr/local/bin
```

Jalankan configure untuk file snort
```
./configure --enable-sourcefire && make && sudo make install
```

------------------------------------------------------------------------------------------------------

## Configuring Snort to run in NIDS mode

Update library config
```
sudo ldconfig
```

Tautkan directory snort  ke /usr/sbin/snort
```
sudo ln -s /usr/local/bin/snort /usr/sbin/snort
```

------------------------------------------------------------------------------------------------------

## Setting up username and folder structure

Buat unprivileged user untuk menjalankan daemon snort
```
sudo groupadd snort
sudo useradd snort -r -s /sbin/nologin -c SNORT_IDS -g snort
```

Buat struktur folder sebagai rumah dari snort
```
sudo mkdir -p /etc/snort/rules
sudo mkdir /var/log/snort
sudo mkdir /usr/local/lib/snort_dynamicrules
```

Set permission direktori tersebut
```
sudo chmod -R 5775 /etc/snort
sudo chmod -R 5775 /var/log/snort
sudo chmod -R 5775 /usr/local/lib/snort_dynamicrules
sudo chown -R snort:snort /etc/snort
sudo chown -R snort:snort /var/log/snort
sudo chown -R snort:snort /usr/local/lib/snort_dynamicrules
```

Buat file rules, whitelist, blacklist, dan local
```
sudo touch /etc/snort/rules/white_list.rules
sudo touch /etc/snort/rules/black_list.rules
sudo touch /etc/snort/rules/local.rules
```

Copy konfigurasi file tersebut ke folder instalasi snort
```
sudo cp ~/snort_src/snort-2.9.20/etc/*.conf* /etc/snort
sudo cp ~/snort_src/snort-2.9.20/etc/*.map /etc/snort
```

------------------------------------------------------------------------------------------------------

## Using community rules

Tambahkan community rules snort menggunakan wget
```
wget https://www.snort.org/rules/community -O ~/community.tar.gz
```

Extract dan copy community rules ke configuration folder
```
sudo tar -xvf ~/community.tar.gz -C ~/
sudo cp ~/community-rules/* /etc/snort/rules
```

Tambahkan path include ke file snort.conf
```
sudo sed -i 's/include $RULE_PATH/#include $RULE_PATH/' /etc/snort/snort.conf
```

------------------------------------------------------------------------------------------------------

## Configuring the network and rule sets

Edit file snort.conf untuk melakukan konfigurasi
```
sudo vi /etc/snort/snort.conf
```

Pada section 1, temukan dan ubah konfigurasi nya sebagai berikut
```
# Setup the network addresses you are protecting
ipvar HOME_NET <ip server>/24
```
```
# Set up the external network addresses. Leave as "any" in most situations
ipvar EXTERNAL_NET !$HOME_NET
```
```
# Path to your rules files (this can be a relative path)
var RULE_PATH /etc/snort/rules
var SO_RULE_PATH /etc/snort/so_rules
var PREPROC_RULE_PATH /etc/snort/preproc_rules
```
```
# Set the absolute path appropriately
var WHITE_LIST_PATH /etc/snort/rules
var BLACK_LIST_PATH /etc/snort/rules
```

Scroll hingga section 6, ubah konfigurasi nya sebagai berikut
```
# unified2
# Recommended for most installs
output unified2: filename snort.log, limit 128
```

Terakhir, scroll hingga section 7, hapus komentar dan tambahkan command berikut
```
include $RULE_PATH/local.rules
```
```
include $RULE_PATH/community.rules
```

------------------------------------------------------------------------------------------------------

## Validating settings

Untuk cek instalasi dan konfigurasi kita berhasil jalankan command berikut
```
sudo snort -T -c /etc/snort/snort.conf
```

Jika snort berhasil diinstall akan muncul
```
--== Initialization Complete ==--

   ,,_ 	-*> Snort! <*-
  o"  )~   Version 2.9.20 GRE (Build 82)
   ''''	By Martin Roesch & The Snort Team: http://www.snort.org/contact#team
       	Copyright (C) 2014-2022 Cisco and/or its affiliates. All rights reserved.
       	Copyright (C) 1998-2013 Sourcefire, Inc., et al.
       	Using libpcap version 1.5.3
       	Using PCRE version: 8.32 2012-11-30
       	Using ZLIB version: 1.2.7


       	Rules Engine: SF_SNORT_DETECTION_ENGINE  Version 3.2  <Build 1>
       	Preprocessor Object: appid  Version 1.1  <Build 5>
       	Preprocessor Object: SF_S7COMMPLUS  Version 1.0  <Build 1>
       	Preprocessor Object: SF_DNP3  Version 1.1  <Build 1>
       	Preprocessor Object: SF_MODBUS  Version 1.1  <Build 1>
       	Preprocessor Object: SF_GTP  Version 1.1  <Build 1>
       	Preprocessor Object: SF_REPUTATION  Version 1.1  <Build 1>
       	Preprocessor Object: SF_SIP  Version 1.1  <Build 1>
       	Preprocessor Object: SF_SDF  Version 1.1  <Build 1>
       	Preprocessor Object: SF_DCERPC2  Version 1.0  <Build 3>
       	Preprocessor Object: SF_SSLPP  Version 1.1  <Build 4>
       	Preprocessor Object: SF_DNS  Version 1.1  <Build 4>
       	Preprocessor Object: SF_SSH  Version 1.1  <Build 3>
       	Preprocessor Object: SF_SMTP  Version 1.1  <Build 9>
       	Preprocessor Object: SF_IMAP  Version 1.0  <Build 1>
       	Preprocessor Object: SF_POP  Version 1.0  <Build 1>
       	Preprocessor Object: SF_FTPTELNET  Version 1.2  <Build 13>

Total snort Fixed Memory Cost - MaxRss:55036
Snort successfully validated the configuration!
```

------------------------------------------------------------------------------------------

## Snort Run Detection
```
sudo snort -A console -q -u snort -g snort -c /etc/snort/snort.conf -i enp0s8
```
