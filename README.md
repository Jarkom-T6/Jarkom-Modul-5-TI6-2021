# Jarkom-Modul-5-TI6-2021

## Anggota Kelompok TI6:

- Syakhisk Al-Azmi 05311940000003
- Muhammad Rizqi Wijaya 05311940000014
- Shafira Firdausi 05311940000040

# Topologi

![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled.png)

## Subnetting VLSM

![vlsm.jpg](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/shift_5.jpg)

### Pembagian NID dan Netmask

![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%201.png)

## Routing

```bash
//Foosha
route add -net 192.214.7.0 netmask 255.255.255.128 gw 192.214.7.146
route add -net 192.214.0.0 netmask 255.255.252.0 gw 192.214.7.146
route add -net 192.214.7.128 netmask 255.255.255.248 gw 192.214.7.146
route add -net 192.214.4.0 netmask 255.255.254.0 gw 192.214.7.150
route add -net 192.214.6.0 netmask 255.255.255.0 gw 192.214.7.150
route add -net 192.214.7.136 netmask 255.255.255.248 gw 192.214.7.150

//Water7
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.214.7.145

//Guanhao
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.214.7.149 
```

## DNS Server

Doriki - DNS Server

```bash
options {
        directory "/var/cache/bind";
        forwarders {
                192.168.122.1;
        };
        allow-query { any; };
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
```

## DHCP Server dan Relay

Jipangu - DHCP Server

```bash
ddns-update-style none;
option domain-name "example.org";
option domain-name-servers ns1.example.org, ns2.example.org;
default-lease-time 600;
max-lease-time 7200;
log-facility local7;
subnet 192.214.0.0 netmask 255.255.252.0 {
    range 192.214.0.2 192.214.3.254;
    option routers 192.214.0.1;
    option broadcast-address 192.214.3.255;
    option domain-name-servers 192.214.7.130;
    default-lease-time 360;
    max-lease-time 7200;
}
subnet 192.214.7.0 netmask 255.255.255.128 {
    range 192.214.7.2 192.214.7.126;
    option routers 192.214.7.1;
    option broadcast-address 192.214.7.127;
    option domain-name-servers 192.214.7.130;
    default-lease-time 720;
    max-lease-time 7200;
}
subnet 192.214.4.0 netmask 255.255.254.0 {
    range 192.214.4.2 192.214.5.254;
    option routers 192.214.4.1;
    option broadcast-address 192.214.5.255;
    option domain-name-servers 192.214.7.130;
    default-lease-time 720;
    max-lease-time 7200;
}
subnet 192.214.6.0 netmask 255.255.255.0 {
    range 192.214.6.2 192.214.6.254;
    option routers 192.214.6.1;
    option broadcast-address 192.214.6.255;
    option domain-name-servers 192.214.7.130;
    default-lease-time 720;
    max-lease-time 7200;
}
subnet 192.214.7.128 netmask 255.255.255.248 {}
subnet 192.214.7.144 netmask 255.255.255.252 {}
subnet 192.214.7.148 netmask 255.255.255.252 {}
subnet 192.214.7.136 netmask 255.255.255.248 {}
```

Water7 - DHCP Relay

```bash
SERVERS="192.214.7.131"
INTERFACES="eth2 eth1 eth3 eth0"
OPTIONS=""
```

Foosha - DHCP Relay

```bash
SERVERS="192.214.7.131"
INTERFACES="eth2 eth1"
OPTIONS=""
```

Guanhao - DHCP Relay

```bash
SERVERS="192.214.7.131"
INTERFACES="eth0 eth3 eth1 eth2"
OPTIONS=""
```

# Soal

## 1

Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.

```bash
//Foosha
IPETH0="$(ip -br a | grep eth0 | awk '{print $NF}' | cut -d'/' -f1)"
iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source "$IPETH0" -s 192.214.0.0/21
```

*Hasil:*

![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%202.png)

## 2

Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.

```bash
//Foosha
iptables -A FORWARD -d 192.214.7.128/29 -i eth0 -p tcp --dport 80 -j DROP
```

*Hasil:*

![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%203.png)

## 3

Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

```bash
//Doriki dan Jipangu

iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

*Hasil:*

- Ping client 1-3

![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%204.png)

![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%205.png)

![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%206.png)

- Ping client ke 4

![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%207.png)

## 4

Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis.

```bash
//Doriki

#Blueno
iptables -A INPUT -s 192.214.7.0/25 -m time --weekdays Fri,Sat,Sun -j REJECT
iptables -A INPUT -s 192.214.7.0/25 -m time --timestart 00:00 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu -j REJECT
iptables -A INPUT -s 192.214.7.0/25 -m time --timestart 15:01 --timestop 23:59 --weekdays Mon,Tue,Wed,Thu -j REJECT

#Cipher
iptables -A INPUT -s 192.214.0.0/22 -m time --weekdays Fri,Sat,Sun -j REJECT
iptables -A INPUT -s 192.214.0.0/22 -m time --timestart 00:00 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu -j REJECT
iptables -A INPUT -s 192.214.0.0/22 -m time --timestart 15:01 --timestop 23:59 --weekdays Mon,Tue,Wed,Thu -j REJECT
```

Hasil:

- Saat mengakses pada jam yang diperbolehkan
    
    ![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%208.png)
    
- Saat mengakses diluar jam yang diperbolehkan
    
    ![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%209.png)
    

## 5

Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.

```bash
//Doriki

#Elena
iptables -A INPUT -s 192.214.4.0/23 -m time --timestart 07:00 --timestop 15:00 -j REJECT

#Fukuro
iptables -A INPUT -s 192.214.6.0/24 -m time --timestart 07:00 --timestop 15:00 -j REJECT
```

Hasil:

- Saat mengakses pada jam yang diperbolehkan
    
    ![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%2010.png)
    
- Saat mengakses diluar jam yang diperbolehkan
    
    ![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%2011.png)
    

## 6

Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate

```bash
//Guanhao

iptables -A PREROUTING -t nat -p tcp -d 192.214.7.130 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.214.7.138:80
iptables -A PREROUTING -t nat -p tcp -d 192.214.7.130 -j DNAT --to-destination 192.214.7.139:80
```

*Hasil:*

![Untitled](Jarkom-Modul-5-TI6-2021%20bbeb5268cd4f46b09df1dfe108bfa97b/Untitled%2012.png)
