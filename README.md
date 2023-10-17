# Jarkom-Modul-2-E22-2023

| Nama                       | NRP        |
| ---------------------------| -----------|
| Anggara Saputra            | 5025211241 |
| Faizah Nurdianti Maghfirah | 5025211134 |

## Soal 1
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut 
### Jawab
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/1.png)
* Router
```
auto eth0
 iface eth0 inet dhcp

 auto eth1
 iface eth1 inet static
 	address 192.217.1.1
 	netmask 255.255.255.0

 auto eth2
 iface eth2 inet static
 	address 192.217.2.1
 	netmask 255.255.255.0
```
* Yudistira
```
auto eth0
 iface eth0 inet static
 	address 192.217.2.4
 	netmask 255.255.255.0
 	gateway 192.217.2.1
```
* Werkudara
```
auto eth0
 iface eth0 inet static
 	address 192.217.2.3
 	netmask 255.255.255.0
 	gateway 192.217.2.1
```
* Arjuna
```
auto eth0
 iface eth0 inet static
 	address 192.217.2.2
 	netmask 255.255.255.0
 	gateway 192.217.2.1
```
* Nakula
```
auto eth0
 iface eth0 inet static
 	address 192.217.1.2
 	netmask 255.255.255.0
 	gateway 192.217.1.1
```
* Sadewa
```
auto eth0
 iface eth0 inet static
 	address 192.217.1.3
 	netmask 255.255.255.0
 	gateway 192.217.1.1
```
* Abimanyu
```
auto eth0
 iface eth0 inet static
 	address 192.217.1.4
 	netmask 255.255.255.0
 	gateway 192.217.1.1
```
* Prabukusuma
```
auto eth0
 iface eth0 inet static
 	address 192.217.1.5
 	netmask 255.255.255.0
 	gateway 192.217.1.1
```
* Wisanggeni
```
auto eth0
 iface eth0 inet static
 	address 192.217.1.6
 	netmask 255.255.255.0
 	gateway 192.217.1.1
```

## Soal 2
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.
### Jawab
1. Jalankan command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.217.0.0/16` Pada node router.
   
2. Jalankan commad berikut pada node YudhistiraDNSMaster untuk instalasi bind9:
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```

3. Masukkan konfigurasi berikut ke dalam /etc/bind/named.conf.local:
```
zone "arjuna.e22.com" {
type master;
file "/etc/bind/jarkom/arjuna.e22.com";
};

zone "2.217.192.in-addr.arpa" {
type master;
file "/etc/bind/jarkom/2.217.192.in-addr.arpa";
};
```

4. Masukkan konfigurasi berikut ke dalam /etc/bind/jarkom/arjuna.e22.com
```
;
; BIND data file for local loopback interface
;
$TTL 604800
@ IN SOA arjuna.e22.com root.arjuna.e22.com (
; Serial
604800 ; Refresh
86400 ; Retry
2419200 ; Expire
604800 ) ; Negative Cache TTL
;
@ IN NS arjuna.e22.com
@ IN A 192.217.2.2
www IN CNAME arjuna.e22.com.
@ IN AAAA ::1
```

5. Nameserver setting lalu ping arjuna.e22.com pada sadewa client
```
nameserver 192.217.2.4
nameserver 192.168.122.1
```

6. Check konfigurasi dns
```
apt-get update
apt-get install dnsutils
host -t PTR 192.217.2.4
```

7. Masukan konfigurasi pada /etc/bindom/2.217.192.in-addr.arpa
```
;
; BIND data file for local loopback interface
;
$TTL 604800
@ IN SOA arjuna.e22.com. root.arjuna.e22.com. (
2 ; Serial
604800 ; Refresh
86400 ; Retry
2419200 ; Expire
604800 ) ; Negative Cache TTL
;
2.217.192.in-addr.arpa IN NS arjuna.e22.com.
4 IN PTR arjuna.e22.com.
" > /etc/bind/jarkom/2.217.192.in-addr.arpa
```

8. Restart bind dan ping
<img width="860" alt="Jepretan Layar 2023-10-17 pukul 19 01 21" src="https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/assets/116636265/599a0d62-f2cd-45e7-852c-03f7ba43916b">

## Soal 3
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.
### Jawab
<img width="860" alt="Jepretan Layar 2023-10-17 pukul 19 00 54" src="https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/assets/116636265/f6b5bd64-c6e9-4af1-add2-42bd3a10ca12">

## Soal 4
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.
### Jawab
1. Ubah konfigruasi pada file /etc/bind/jarkom/abimanyu.e22.com pada YudhistiraDNSMaster:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.e22.com. root.abimanyu.e22.com. (
                             2          ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.e22.com.
@       IN      A       192.217.1.4
www     IN      CNAME   abimanyu.e22.com.
parikesit       IN      A       192.217.1.4
```

2. Lakukan ping pada client, sebagai contoh adalah NakulaClient:
<img width="878" alt="Jepretan Layar 2023-10-17 pukul 19 01 50" src="https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/assets/116636265/52fb5312-afe4-4704-b685-da4410fac5c6">

## Soal 5
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)
### Jawab
1. Masukkan konfigurasi berikut pada file /etc/bind/named.conf.local pada node YudhistiraDNSMaster:
```
zone "arjuna.e22.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.e22.com";
};

zone "abimanyu.e22.com" {
        type master;
        file "/etc/bind/jarkom/abimanyu.e22.com";
};

zone "2.217.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.217.192.in-addr.arpa";
};

zone "1.217.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/1.217.192.in-addr.arpa";
};
```

2. Masukkan konfigurasi berikut pada file /etc/bind/jarkom/2.217.192.in-addr.arpa:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.e22.com. root.arjuna.e22.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.217.192.in-addr.arpa.  IN      NS      arjuna.e22.com.
2                       IN      PTR     arjuna.e22.com.
```

3. Masukkan konfigurasi berikut pada file /etc/bind/jarkom/1.217.192.in-addr.arpa:
```
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.e22.com. root.abimanyu.e22.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
1.217.192.in-addr.arpa.  IN      NS      abimanyu.e22.com.
4                       IN      PTR     abimanyu.e22.com.
```
   
4. Lakukan restart bind9: `service bind9 restart`
   
5. Lakukan instalasi dnsutils pada client dengan script berikut:
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install dnsutils
echo nameserver 192.217.2.4 > /etc/resolv.conf
```

6. Lakukan testing host sebagai berikut:
```
host -t PTR 192.217.2.2 (IP ArjunaLoadBalancer)
host -t PTR 192.217.1.4 (IP AbimanyuWebServer)
```
<img width="878" alt="Jepretan Layar 2023-10-17 pukul 19 02 37" src="https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/assets/116636265/6d11b8b6-afaa-4c8d-8f6d-611ea6a11f2e">

## Soal 6
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
### Jawab
1. Masukkan konfigurasi berikut pada file /etc/bind/named.conf.local pada node YudhistiraDNSMaster:
```
zone "arjuna.e22.com" {
        type master;
        notify yes;
        also-notify { 192.217.2.3; }; // IP Werkudara
        allow-transfer { 192.217.2.3; }; // IP Werkudara
        file "/etc/bind/jarkom/arjuna.e22.com";
};

zone "abimanyu.e22.com" {
        type master;
        notify yes;
        also-notify { 192.217.2.3; }; // IP Werkudara
        allow-transfer { 192.217.2.3; }; // IP Werkudara
        file "/etc/bind/jarkom/abimanyu.e22.com";
};

zone "2.217.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.217.192.in-addr.arpa";
};

zone "1.217.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/1.217.192.in-addr.arpa";
};
```

2. Kemudian lakukan restart bind9: `service bind9 restart`
   
3. Kemudian instalasi bind9 berikut pada WerkudaraDNSSlave:
```
echo nameserver 192.168.122.1 > /etc/bind/named.conf.local
apt-get update
pt-get install bind9 -y
```

4. Kemudian konfigurasi /etc/bind/named.conf.local pada node WerkudaraDNSSlave:
```
zone "arjuna.e22.com" {
        type slave;
        masters { 192.217.2.4; }; // IP Yudhistira
        file "/var/lib/bind/arjuna.e22.com";


zone "abimanyu.e22.com" {
        type slave;
        masters { 192.217.2.4; }; // IP Yudhistira
        file "/var/lib/bind/abimanyu.e22.com";
};
```

5. Lakukan restart bind9 pada WerkudaraDNSSlave: `service bind9 restart`

6. Matikan bind9 pada YudhistiraDNSMaster: `service bind9 stop`
   
7. Lakukan testing pada client, sebagai contoh pada NakulaClient: 
```
echo nameserver 192.217.2.3 > /etc/resolv.conf
ping arjuna.e22.com
ping abimanyu.e22.com
```
<img width="882" alt="Jepretan Layar 2023-10-17 pukul 19 03 23" src="https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/assets/116636265/f64a86ed-a118-4156-84a4-bd920a06bc77">

## Soal 7
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.
### Jawab
* Yudhistira
```
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.e22.com. root.abimanyu.e22.com. (
                             2          ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      abimanyu.e22.com.
@               IN      A       192.217.1.4         ; IP Abimanyu
www             IN      CNAME   abimanyu.e22.com.
parikesit       IN      A       192.217.1.4         ; IP Abimanyu
ns1             IN      A       192.217.2.2         ; IP Werkudara
baratayuda      IN      NS      ns1
@               IN      AAAA    ::1

' > /etc/bind/jarkom/abimanyu.e22.com
```
```
echo '
options {
        directory \"/var/cache/bind\";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0\'s placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
	    allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};"

' > /etc/bind/named.conf.options
```
```
service bind9 restart
```
* Werkudara
```
echo '
options {
        directory \"/var/cache/bind\";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0\'s placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
	    allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};"

' > /etc/bind/named.conf.options
```
```
echo '
zone "baratayuda.abimanyu.e22.com" {
       type master;
       file "/etc/bind/baratayuda/baratayuda.abimanyu.e22.com";
};

' >> /etc/bind/named.conf.local
```
```
mkdir /etc/bind/baratayuda
```
```
echo '
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.e22.com. root.baratayuda.abimanyu.e22.com. (
                        2      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@               IN      NS      baratayuda.abimanyu.e22.com.
@               IN      A       192.217.1.4       ; IP Abimanyu
www             IN      CNAME   baratayuda.abimanyu.e22.com.

' > /etc/bind/baratayuda/baratayuda.abimanyu.e22.com
```
```
service bind9 restart
```
* Client
```
ping baratayuda.abimanyu.e22.com
ping www.baratayuda.abimanyu.e22.com
```
## Soal 7
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.
### Jawab
* Yudhistira
```
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.e22.com. root.abimanyu.e22.com. (
                             2          ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      abimanyu.e22.com.
@               IN      A       192.217.1.4         ; IP Abimanyu
www             IN      CNAME   abimanyu.e22.com.
parikesit       IN      A       192.217.1.4         ; IP Abimanyu
ns1             IN      A       192.217.2.2         ; IP Werkudara
baratayuda      IN      NS      ns1
@               IN      AAAA    ::1

' > /etc/bind/jarkom/abimanyu.e22.com
```
```
echo '
options {
        directory \"/var/cache/bind\";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0\'s placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
	    allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};"

' > /etc/bind/named.conf.options
```
```
service bind9 restart
```
* Werkudara
```
echo '
options {
        directory \"/var/cache/bind\";

        // If there is a firewall between you and nameservers you want
        // to talk to, you may need to fix the firewall to allow multiple
        // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

        // If your ISP provided one or more IP addresses for stable
        // nameservers, you probably want to use them as forwarders.
        // Uncomment the following block, and insert the addresses replacing
        // the all-0\'s placeholder.

        // forwarders {
        //      0.0.0.0;
        // };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
	    allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};"

' > /etc/bind/named.conf.options
```
```
echo '
zone "baratayuda.abimanyu.e22.com" {
       type master;
       file "/etc/bind/baratayuda/baratayuda.abimanyu.e22.com";
};

' >> /etc/bind/named.conf.local
```
```
mkdir /etc/bind/baratayuda
```
```
echo '
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.e22.com. root.baratayuda.abimanyu.e22.com. (
                        2      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@               IN      NS      baratayuda.abimanyu.e22.com.
@               IN      A       192.217.1.4       ; IP Abimanyu
www             IN      CNAME   baratayuda.abimanyu.e22.com.

' > /etc/bind/baratayuda/baratayuda.abimanyu.e22.com
```
```
service bind9 restart
```
* Client
```
ping baratayuda.abimanyu.e22.com
ping www.baratayuda.abimanyu.e22.com
```
## Soal 8
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.
### Jawab
* Werkudara
```
echo '
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.e22.com. root.baratayuda.abimanyu.e22.com. (
                        2      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@               IN      NS      baratayuda.abimanyu.e22.com.
@               IN      A       192.217.1.4       ; IP Abimanyu
www             IN      CNAME   baratayuda.abimanyu.e22.com.
rjp             IN      A       192.217.1.4       ; IP Abimanyu
www.rjp         IN      CNAME   baratayuda.abimanyu.e22.com.

' > /etc/bind/baratayuda/baratayuda.abimanyu.e22.com
```
```
service bind9 restart
```
* Client
```
ping rjp.baratayuda.abimanyu.e22.com
ping www.rjp.baratayuda.abimanyu.e22.com
```
## Soal 9
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.
### Jawab
* Arjuna
```
echo "
nameserver 192.168.122.1
nameserver 192.217.2.4
" >/etc/resolv.conf
```
```
apt-get update
apt-get install bind9 nginx -y
service nginx start
```
```
echo "
upstream webE22  {
  server 192.217.1.4; # IP Abimanyu
  server 192.217.1.5; # IP Prabukusuma
  server 192.217.1.6; # IP Wisanggeni
}

server {
  listen 80;
  server_name arjuna.E22.com www.arjuna.E22.com;
  location / {
  proxy_pass http://webE22;
  }
}
" >/etc/nginx/sites-available/soal9
```
```
ln -s /etc/nginx/sites-available/soal9 /etc/nginx/sites-enabled
rm /etc/nginx/sites-enabled/default
```
```
service nginx restart
```
* Webserver
```
echo '
nameserver 192.168.122.1
nameserver 192.217.2.4
' > /etc/resolv.conf
```
```
apt-get update
apt-get install nginx php php-fpm -y
apt-get install unzip
```
```
wget -O '/var/www/arjuna.e22.com.zip' 'https://drive.usercontent.google.com/download?id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX'
unzip /var/www/arjuna.e22.com.zip -d /var/www/
mv /var/www/arjuna.yyy.com /var/www/arjuna.e22
rm /var/www/arjuna.e22.com.zip
```
```
service nginx start
service php7.2-fpm start
```
```
echo "
server {

	listen 80;

	root /var/www/arjuna.e22;

	index index.php index.html index.htm;
	server_name arjuna.e22.com;

	location / {
			try_files $uri $uri/ /index.php?$query_string;
	}

	# pass PHP scripts to FastCGI server
	location ~ \.php$ {
	include snippets/fastcgi-php.conf;
	fastcgi_pass unix:/run/php/php7.2-fpm.sock;
	}

location ~ /\.ht {
			deny all;
	}

	error_log /var/log/nginx/jarkom_error.log;
	access_log /var/log/nginx/jarkom_access.log;
}'

" > /etc/nginx/sites-available/soal9
```
```
ln -s /etc/nginx/sites-available/soal9 /etc/nginx/sites-enabled
rm /etc/nginx/sites-enabled/default

service nginx restart
```
* Client
```
apt-get install lynx -y
```
```
lynx arjuna.e22.com
```

## Soal 10
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003
### Jawab
* Arjuna
```
echo "
# Round Robin
upstream webE22  {
  server 192.217.1.4:8001; # IP Abimanyu
  server 192.217.1.5:8002; # IP Prabukusuma
  server 192.217.1.6:8003; # IP Wisanggeni
}

server {
  listen 80;
  server_name arjuna.E22.com www.arjuna.E22.com;
  location / {
  proxy_pass http://webE22;
  }
}
" >/etc/nginx/sites-available/soal10
```
```
ln -s /etc/nginx/sites-available/soal10 /etc/nginx/sites-enabled
service nginx restart
```
* Webserver

Ubah port server pada setiap webserver sebagai berikut

Abimanyu `8001`

Prabukusuma `8002`

Wisanggeni `8003`

## REVISI
## Soal 11
Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy
### Jawab
* Abimanyu
```
apt-get install apache2 -y
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/abimanyu.e22.conf
wget -O '/var/www/abimanyu.e22.com.zip' 'https://drive.usercontent.google.com/download?id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc'
unzip /var/www/abimanyu.e22.com.zip -d /var/www/
mv /var/www/abimanyu.yyy.com /var/www/abimanyu.e22.com
rm /var/www/abimanyu.e22.com.zip
```
```
mkdir /var/www/abimanyu.e22
```
```
echo "<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/abimanyu.e22
	ServerName abimanyu.e22.com
	ServerAlias www.abimanyu.e22.com

    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined

</VirtualHost>" > /etc/apache2/sites-available/abimanyu.e22.com.conf
```
```
a2ensite abimanyu.e22.com.conf
service apache2 restart
```
* Client
```
lynx abimanyu.e22.com
lynx abimanyu.e22.com/index.php/home
```
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/11.lynxabimanyu.e22.com.png)
## Soal 12
Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.
### Jawab
* Abimanyu
```
echo "<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/abimanyu.e22
	ServerName abimanyu.e22.com
	ServerAlias www.abimanyu.e22.com

	<Directory /var/www/abimanyu.e22/index.php/home>
    	Options +Indexes
	</Directory>

	Alias \"/home\" \"/var/www/abimanyu.e22/index.php/home\"


    ErrorLog \${APACHE_LOG_DIR}/error.log
    CustomLog \${APACHE_LOG_DIR}/access.log combined

</VirtualHost>" > /etc/apache2/sites-available/abimanyu.e22.com.conf
```
```
a2ensite abimanyu.e22.com.conf
service apache2 restart
```
* Client
```
lynx abimanyu.e22.com/home
```
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/12.lynxabimanyu.e22.com-home.png)
## Soal 13
Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy
### Jawab
* Abimanyu
```
mkdir /var/www/parikesit.abimanyu.e22
```
```
echo "
<VirtualHost *:80>
  ServerName parikesit.abimanyu.e22.com
  ServerAlias www.parikesit.abimanyu.e22.com
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.e22

  ErrorLog \${APACHE_LOG_DIR}/error.log
  CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>" >/etc/apache2/sites-available/parikesit.abimanyu.e22.com.conf
```
```
wget -O '/var/www/parikesit.abimanyu.e22.com.zip' 'https://drive.usercontent.google.com/download?id=1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS'
unzip /var/www/parikesit.abimanyu.e22.com.zip -d /var/www/
rm -r /var/www/parikesit.abimanyu.e22
mv /var/www/parikesit.abimanyu.yyy.com /var/www/parikesit.abimanyu.e22
rm /var/www/parikesit.abimanyu.e22.com.zip
```
```
a2ensite parikesit.abimanyu.e22.com.conf
service apache2 reload
service apache2 restart
```
* Client
```
lynx abimanyu.e22.com/home
```
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/13.lynxparikesit.abimanyu.e22.com.png)
## Soal 14
Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).
### Jawab
* Abimanyu
```
mkdir /var/www/parikesit.abimanyu.e22/secret
cp /var/www/parikesit.abimanyu.e22/error/403.html /var/www/parikesit.abimanyu.e22/secret/403.html
```
```
echo "
<VirtualHost *:80>
  	ServerName parikesit.abimanyu.e22.com
  	ServerAlias www.parikesit.abimanyu.e22.com
  	ServerAdmin webmaster@localhost
  	DocumentRoot /var/www/parikesit.abimanyu.e22

  	<Directory /var/www/parikesit.abimanyu.e22/public>
		Options +Indexes
	</Directory>
	<Directory /var/www/parikesit.abimanyu.e22/secret>
  		Options -Indexes
	</Directory>

  	ErrorLog \${APACHE_LOG_DIR}/error.log
  	CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>" >/etc/apache2/sites-available/parikesit.abimanyu.e22.com.conf
```
```
a2ensite parikesit.abimanyu.e22.com.conf
service apache2 restart
```
* Client
```
lynx parikesit.abimanyu.e22.com/public
lynx parikesit.abimanyu.e22.com/secret
```
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/14.lynxparikesit.abimanyu.e22.com-public.png)
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/14.lynxparikesit.abimanyu.e22.com-secret-1.png)
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/14.lynxparikesit.abimanyu.e22.com-secret-2.png)
## Soal 15
Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.
### Jawab
* Abimanyu
```
echo "
<VirtualHost *:80>
  	ServerName parikesit.abimanyu.e22.com
  	ServerAlias www.parikesit.abimanyu.e22.com
  	ServerAdmin webmaster@localhost
  	DocumentRoot /var/www/parikesit.abimanyu.e22

	ErrorDocument 404 /error/404.html
	ErrorDocument 403 /error/403.html

  	<Directory /var/www/parikesit.abimanyu.e22/public>
		Options +Indexes
	</Directory>
	<Directory /var/www/parikesit.abimanyu.e22/secret>
  		Options -Indexes
	</Directory>

  	ErrorLog \${APACHE_LOG_DIR}/error.log
  	CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>" >/etc/apache2/sites-available/parikesit.abimanyu.e22.com.conf
```
```
a2ensite parikesit.abimanyu.e22.com.conf
service apache2 restart
```
* Client
```
lynx parikesit.abimanyu.e22.com/secret
```
```
lynx parikesit.abimanyu.e22.com/sembarang
```
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/15.lynxparikesit.abimanyu.e22.com-secret-1.png)
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/15.lynxparikesit.abimanyu.e22.com-secret-2.png)
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/15.lynxparikesit.abimanyu.e22.com-sembarang-1.png)
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/15.lynxparikesit.abimanyu.e22.com-sembarang-2.png)
## Soal 16
Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi 
www.parikesit.abimanyu.yyy.com/js 
### Jawab
* Abimanyu
```
echo "
<VirtualHost *:80>
  	ServerName parikesit.abimanyu.e22.com
  	ServerAlias www.parikesit.abimanyu.e22.com
  	ServerAdmin webmaster@localhost
  	DocumentRoot /var/www/parikesit.abimanyu.e22

	ErrorDocument 404 /error/404.html
	ErrorDocument 403 /error/403.html

  	<Directory /var/www/parikesit.abimanyu.e22/public>
		Options +Indexes
	</Directory>
	<Directory /var/www/parikesit.abimanyu.e22/secret>
  		Options -Indexes
	</Directory>

	Alias "/js" "/var/www/parikesit.abimanyu.e22/public/js"

  	ErrorLog \${APACHE_LOG_DIR}/error.log
  	CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>" >/etc/apache2/sites-available/parikesit.abimanyu.e22.com.conf
```
```
a2ensite parikesit.abimanyu.e22.com
service apache2 restart
```
* Client
```
lynx parikesit.abimanyu.e22.com/js
lynx www.parikesit.abimanyu.e22.com/js
```
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/16.lynxparikesit.abimanyu.e22.com-js.png)
## Soal 17
Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.
### Jawab
* Abimanyu
```
mkdir /var/www/rjp.baratayuda.abimanyu.e22
```
```
echo -e '<VirtualHost *:14000 *:14400>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/rjp.baratayuda.abimanyu.e22
  ServerName rjp.baratayuda.abimanyu.e22.com
  ServerAlias www.rjp.baratayuda.abimanyu.e22.com

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.e22.com.conf
```
```
echo -e '# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 80
Listen 14000
Listen 14400

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet' > /etc/apache2/ports.conf
```
```
wget -O '/var/www/rjp.baratayuda.abimanyu.e22.com.zip' 'https://drive.usercontent.google.com/download?id=1pPSP7yIR05JhSFG67RVzgkb-VcW9vQO6'
unzip /var/www/rjp.baratayuda.abimanyu.e22.com.zip -d /var/www/
rm -r /var/www/rjp.baratayuda.abimanyu.e22
mv /var/www/rjp.baratayuda.abimanyu.yyy.com /var/www/rjp.baratayuda.abimanyu.e22
rm /var/www/rjp.baratayuda.abimanyu.e22.com.zip
```
```
a2ensite rjp.baratayuda.abimanyu.e22.com.conf
service apache2 restart
```
* Client
```
lynx rjp.baratayuda.abimanyu.e22.com:14000
lynx rjp.baratayuda.abimanyu.e22.com:14400
```
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/17.lynxrjp.baratayuda.abimanyu.e22.com-14000.png)
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/17.lynxrjp.baratayuda.abimanyu.e22.com-14400.png)
## Soal 18
Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.
### Jawab
* Abimanyu
```
echo -e '<VirtualHost *:14000 *:14400>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/rjp.baratayuda.abimanyu.e22
  ServerName rjp.baratayuda.abimanyu.e22.com
  ServerAlias www.rjp.baratayuda.abimanyu.e22.com

  <Directory /var/www/rjp.baratayuda.abimanyu.e22>
          AuthType Basic
          AuthName "Restricted Content"
          AuthUserFile /etc/apache2/.htpasswd
          Require valid-user
  </Directory>

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.e22.com.conf
```
```
htpasswd -c -b /etc/apache2/.htpasswd Wayang baratayudae22
```
```
a2ensite rjp.baratayuda.abimanyu.e22.com.conf
service apache2 restart
```
* Client
```
lynx rjp.baratayuda.abimanyu.e22.com:14000
lynx rjp.baratayuda.abimanyu.e22.com:14400
```
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/18.lynxrjp.baratayuda.abimanyu.e22.com-14400-baratayudae22.png)
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/18.lynxrjp.baratayuda.abimanyu.e22.com-14400-Wayang.png)
## Soal 19
Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)
### Jawab
* Abimanyu
```
echo -e '<VirtualHost *:80>
    ServerAdmin webmaster@abimanyu.e22.com
    DocumentRoot /var/www/html

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    Redirect / http://www.abimanyu.e22.com/
</VirtualHost>' > /etc/apache2/sites-available/000-default.conf
```
```
apache2ctl configtest
a2ensite 000-default.conf
service apache2 restart
```
* Client
```
lynx 192.217.1.4
```
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/11.lynxabimanyu.e22.com.png)
## Soal 20
Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.
### Jawab
* Abimanyu
```
a2enmod rewrite
```
```
echo 'RewriteEngine On
RewriteCond %{REQUEST_URI} ^/public/images/(.*)(abimanyu)(.*\.(png|jpg))
RewriteCond %{REQUEST_URI} !/public/images/abimanyu.png
RewriteRule abimanyu http://parikesit.abimanyu.e22.com/public/images/abimanyu.png$1 [L,R=301]' > /var/www/parikesit.abimanyu.e22/.htaccess
```
```
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.e22

  ServerName parikesit.abimanyu.e22.com
  ServerAlias www.parikesit.abimanyu.e22.com

  <Directory /var/www/parikesit.abimanyu.e22/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.e22/secret>
          Options -Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.e22>
          Options +FollowSymLinks -Multiviews
          AllowOverride All
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.e22/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.e22/secret"
  Alias "/js" "/var/www/parikesit.abimanyu.e22/public/js"

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.e22.com.conf
```
```
service apache2 restart
```
* Client
```
lynx parikesit.abimanyu.D06.com/public/images/not-abimanyu.png
```
![image](https://github.com/fzhmghfrh/Jarkom-Modul-2-E22-2023/blob/main/Image/20.lynxparikesit.abimanyu.e22.com-public-images-not-abimanyu.png)

## Kendala
