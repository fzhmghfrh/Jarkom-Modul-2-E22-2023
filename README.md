# Jarkom-Modul-2-E22-2023

| Nama                       | NRP        |
| ---------------------------| -----------|
| Anggara Saputra            | 5025211241 |
| Faizah Nurdianti Maghfirah | 5025211134 |

## Soal 1
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut 
### Jawab
[]
## Soal 2
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.
### Jawab
1. Jalankan command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.217.0.0/16` Pada node router.
2. Jalankan commad berikut pada node YudhistiraDNSMaster untuk instalasi bind9:
`echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install bind9 -y`
3. Masukkan konfigurasi berikut ke dalam /etc/bind/named.conf.local:
`zone "arjuna.e22.com" {
type master;
file "/etc/bind/jarkom/arjuna.e22.com";
};`

`zone "2.217.192.in-addr.arpa" {
type master;
file "/etc/bind/jarkom/2.217.192.in-addr.arpa";
};`
4. Masukkan konfigurasi berikut ke dalam /etc/bind/jarkom/arjuna.e22.com
`;
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
@ IN AAAA ::1`
5. Nameserver setting lalu ping arjuna.e22.com pada sadewa client
`nameserver 192.217.2.4
nameserver 192.168.122.1`
6. Check konfigurasi dns
`apt-get update
apt-get install dnsutils
host -t PTR 192.217.2.4`
7. Masukan konfigurasi pada /etc/bindom/2.217.192.in-addr.arpa
`;
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
" > /etc/bind/jarkom/2.217.192.in-addr.arpa`
8. Restart bind dan ping 
[]
## Soal 3
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.
### Jawab
[]
## Soal 4
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.
### Jawab
1. Ubah konfigruasi pada file /etc/bind/jarkom/abimanyu.e22.com pada YudhistiraDNSMaster:
`;
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
parikesit       IN      A       192.217.1.4`
2. Lakukan ping pada client, sebagai contoh adalah NakulaClient:
[]
## Soal 5
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)
### Jawab
1. Masukkan konfigurasi berikut pada file /etc/bind/named.conf.local pada node YudhistiraDNSMaster:
`zone "arjuna.e22.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.e22.com";
};`

`zone "abimanyu.e22.com" {
        type master;
        file "/etc/bind/jarkom/abimanyu.e22.com";
};`

`zone "2.217.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.217.192.in-addr.arpa";
};`

`zone "1.217.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/1.217.192.in-addr.arpa";
};`
2. Masukkan konfigurasi berikut pada file /etc/bind/jarkom/2.217.192.in-addr.arpa:
`;
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
`
3. Masukkan konfigurasi berikut pada file /etc/bind/jarkom/1.217.192.in-addr.arpa:
`; BIND data file for local loopback interface
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
4                       IN      PTR     abimanyu.e22.com.`
4. Lakukan restart bind9: `service bind9 restart`
5. Lakukan instalasi dnsutils pada client dengan script berikut:
`echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install dnsutils
echo nameserver 192.217.2.4 > /etc/resolv.conf`
6. Lakukan testing host sebagai berikut:
`host -t PTR 192.217.2.2 (IP ArjunaLoadBalancer)
host -t PTR 192.217.1.4 (IP AbimanyuWebServer)`
[]
## Soal 6
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
### Jawab
1. Masukkan konfigurasi berikut pada file /etc/bind/named.conf.local pada node YudhistiraDNSMaster:
`zone "arjuna.e22.com" {
        type master;
        notify yes;
        also-notify { 192.217.2.3; }; // IP Werkudara
        allow-transfer { 192.217.2.3; }; // IP Werkudara
        file "/etc/bind/jarkom/arjuna.e22.com";
};`

`zone "abimanyu.e22.com" {
        type master;
        notify yes;
        also-notify { 192.217.2.3; }; // IP Werkudara
        allow-transfer { 192.217.2.3; }; // IP Werkudara
        file "/etc/bind/jarkom/abimanyu.e22.com";
};`

`zone "2.217.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.217.192.in-addr.arpa";
};`

`zone "1.217.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/1.217.192.in-addr.arpa";
};`
2. Kemudian lakukan restart bind9: `service bind9 restart`
3. Kemudian instalasi bind9 berikut pada WerkudaraDNSSlave:
`echo nameserver 192.168.122.1 > /etc/bind/named.conf.local
apt-get update
pt-get install bind9 -y`
4. Kemudian konfigurasi /etc/bind/named.conf.local pada node WerkudaraDNSSlave:
`zone "arjuna.e22.com" {
        type slave;
        masters { 192.217.2.4; }; // IP Yudhistira
        file "/var/lib/bind/arjuna.e22.com";
};`

`zone "abimanyu.e22.com" {
        type slave;
        masters { 192.217.2.4; }; // IP Yudhistira
        file "/var/lib/bind/abimanyu.e22.com";
};`
5. Lakukan restart bind9 pada WerkudaraDNSSlave: `service bind9 restart`
6. Matikan bind9 pada YudhistiraDNSMaster: `service bind9 stop`
7. Lakukan testing pada client, sebagai contoh pada NakulaClient: 
`echo nameserver 192.217.2.3 > /etc/resolv.conf
ping arjuna.e22.com
ping abimanyu.e22.com`
[]
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
## Soal 9
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.
### Jawab
## Soal 10
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003
### Jawab
## Soal 11
Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy
### Jawab
## Soal 12
Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.
### Jawab
## Soal 13
Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy
### Jawab
## Soal 14
Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).
### Jawab
## Soal 15
Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.
### Jawab
## Soal 16
Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi 
www.parikesit.abimanyu.yyy.com/js 
### Jawab
## Soal 17
Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.
### Jawab
## Soal 18
Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.
### Jawab
## Soal 19
Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)
### Jawab
## Soal 20
Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.
### Jawab





Revisi



## Kendala
