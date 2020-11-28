# Modul 3 : DHCP dan Proxy Server
Nama Anggota :

- 05111840000093 Muhammad Afif Fadhlurrahman
- 05111740000091 Affan Ahsanul Habib

Session :
- [DHCP](#dhcp)
   * [No 1](#nomor-1)
   * [No 2](#nomor-2)
   * [No 3](#nomor-3)
   * [No 4](#nomor-4)
   * [No 5](#nomor-5)
   * [No 6](#nomor-6)
- [Proxy Server](#proxy-server)
   * [No 7](#nomor-7)
   * [No 8](#nomor-8)
   * [No 9](#nomor-9)
   * [No 10](#nomor-10)
   * [No 11](#nomor-11)
   * [No 12](#nomor-12)

## DHCP

#### Nomor 1
**1. Membuat Topologi** </br>

Jawab : </br>

a. Jalankan Xming kemudian jalankan PuTTY seperti pada [Modul Pengenalan UML](https://github.com/arsitektur-jaringan-komputer/Modul-Jarkom/tree/modul-uml) </br>

b. Setelah login, buat file script dengan ekstensi **.sh** yang akan digunakan untuk menyimpan script membuat **router**, **switch**, dan **clienT**. Misalkan bernama **topo.sh**. Sehingga ketik `nano topo.sh`</br>

c. Sintaks yang digunakan adalah sebagai berikut: </br>

![No 1 Buat topo.sh](/img/1-topo.png)

```
# Switch
uml_switch -unix switch1 > /dev/null < /dev/null &
uml_switch -unix switch2 > /dev/null < /dev/null &
uml_switch -unix switch3 > /dev/null < /dev/null &

# Router
xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.78.9 eth1=daemon,,,switch1 eth2=daemon,,,switch3 eth3=daemon,,,switch2  mem=256M &

# Server
xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=160M &
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &
xterm -T TUBAN -e linux ubd0=TUBAN,jarkom umid=TUBAN eth0=daemon,,,switch2 mem=128M &

# Klien
xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch1 mem=64M &
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch1 mem=64M &
xterm -T BANYUWANGI -e linux ubd0=BANYUWANGI,jarkom umid=BANYUWANGI eth0=daemon,,,switch3 mem=64M &
xterm -T MADIUN -e linux ubd0=MADIUN,jarkom umid=MADIUN eth0=daemon,,,switch3 mem=64M &

```

</br>

d. Jalankan script **topo.sh** dengan perintah `bash topologi.sh`. Kemudian login pada setiap UML. </br>

e. Pada router **SURABAYA** lakukan setting sysctl dengan mengetikkan perintah `nano /etc/sysctl.conf` </br>

f. Hilangkan tanda pagar (#) pada bagian `net.ipv4.ip_forward=1`. Lalu ketikkan `sysctl -p` untuk mengaktifkan perubahan yang ada. </br>

![No 1 sysctl](/img/1-sysctl.png)

g. Setting IP pada setiap UML dengan mengetikkan `nano /etc/network/interfaces`. Lalu setting IP-nya sebagai berikut: </br>

**SURABAYA (Sebagai Router)** </br>

```
auto eth0
iface eth0 inet static
address 10.151.78.10
netmask 255.255.255.252
gateway 10.151.78.9

auto eth1
iface eth1 inet static
address 192.168.0.1
netmask 255.255.255.0

auto eth2
iface eth2 inet static
address 192.168.1.1
netmask 255.255.255.0

auto eth3
iface eth3 inet static
address 10.151.79.17
netmask 255.255.255.248
```

**MALANG (DNS Server)** </br>

```
auto eth0
iface eth0 inet static
address 10.151.79.18
netmask 255.255.255.248
gateway 10.151.79.17
```

**MOJOKERTO (Proxy Server)** </br>

```
auto eth0
iface eth0 inet static
address 10.151.79.19
netmask 255.255.255.248
gateway 10.151.79.17
```

**TUBAN (DHCP Server)** </br>

```
auto eth0
iface eth0 inet static
address 10.151.79.20
netmask 255.255.255.248
gateway 10.151.79.17
```

**SIDOARJO (Client)** </br>

```
auto eth0
iface eth0 inet static
address 192.168.0.2
netmask 255.255.255.0
gateway 192.168.0.1
```

**GRESIK (Client)** </br>

```
auto eth0
iface eth0 inet static
address 192.168.0.3
netmask 255.255.255.0
gateway 192.168.0.1
```

**BANYUWANGI (Client)** </br>

```
auto eth0
iface eth0 inet static
address 192.168.1.2
netmask 255.255.255.0
gateway 192.168.1.1
```

**MADIUN (Client)** </br>

```
auto eth0
iface eth0 inet static
address 192.168.1.3
netmask 255.255.255.0
gateway 192.168.1.1
```

h. Restart network dengan mengetikkan `service networking restart` di setiap UML. </br>

i. Ketikkan `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.168.0.0/16` pada router SURABAYA. </br>

k. Export proxy pada setiap UML dengan sintaks seperti di bawah ini: </br>
```
export http_proxy=”http://[username-vpn]:[password]@proxy.its.ac.id:8080”
export https_proxy=”http://[username-vpn]:[password]@proxy.its.ac.id:8080”
export ftp_proxy=”http://[username-vpn]:[password]@proxy.its.ac.id:8080”
```

</br>

Namun, kami membuat file **proxy.sh** yang berisi sintaks tersebut pada setiap UML. Sehingga tinggal mengetik `source proxy.sh` ketika ingin export proxy.

#### Nomor 2
**2. SURABAYA ditunjuk sebagai perantara (DHCP Relay) antara DHCP Server dan client.**  </br>

Jawab : </br>

**Pada UML TUBAN** </br>

a. Install **isc-dhcp-server** dengan perintah `apt-get install isc-dhcp-server`. </br>

b. Kemudian jalankan perintah `nano /etc/dhcp/dhcpd.conf`, edit rules menjadi sebagai berikut: </br>

![No 2 config DHCP Server](/img/2-config-tuban-1.png)

![No 2 config DHCP Server](/img/2-config-tuban-2.png)

```
subnet 10.151.79.16 netmask 255.255.255.248 {   #adalah subnet network server dan router# 
  range 10.151.79.18 10.151.79.20;
  option routers 10.151.79.17;
  option broadcast-address 10.151.79.255;
  option domain-name-servers 10.151.79.18, 202.46.129.2;
}

#subnet1 5menit
subnet 192.168.0.0 netmask 255.255.255.0 {  #adalah subnet network router dan client# 
  range 192.168.0.10 192.168.0.100;
  range 192.168.0.110 192.168.0.200;
  option routers 192.168.0.1;
  option broadcast-address 192.168.0.255;
  option domain-name-servers 10.151.79.18, 202.46.129.2;
  default-lease-time 300;
  max-lease-time 3000;
}

#subnet3 10menit
subnet 192.168.1.0 netmask 255.255.255.0 {  #adalah subnet network router dan client# 
  range 192.168.1.50 192.168.1.70;
  option routers 192.168.1.1;
  option broadcast-address 192.168.1.255;
  option domain-name-servers 10.151.79.18, 202.46.129.2;
  default-lease-time 600;
  max-lease-time 6000;
}
```

</br>

c. Save kemudian restart dengan perintah `service isc-dhcp-server restart`. </br>

**Pada UML SURABAYA** </br>

a. Install **isc-dhcp-relay** dengan perintah `apt-get install isc-dhcp-relay`. </br>

b. Kemudian jalankan perintah `nano /etc/default/isc-dhcp-relay`, edit menjadi sebagai berikut: </br>

![No 2 config DHCP Relay](/img/2-config-sby.png)

```
SERVERS="10.151.79.20"
```
```
INTERFACES="eth1 eth2 eth3"
```

</br>

c. Save kemudian restart dengan perintah `service isc-dhcp-relay restart`. </br>

**Pada Setiap Client** </br>

a. Ketik `nano /etc/network/interfaces`</br>

b. Comment pada konfigursasi IP statis. Tambahkan </br>

```
auto eth0
iface eth0 inet dhcp
```

</br>

![No 2 interface client](/img/2-interface-client.png)

c. Kemudian restart dengan `service networking restart` </br>

d. Testing </br>

![No 2 ifconfig client](/img/2-ifconfig-client.png)

</br>

#### Nomor 3
**3. Client pada subnet 1 mendapatkan range IP dari 192.168.0.10 sampai 192.168.0.100 dan 192.168.0.110 sampai 192.168.0.200.**  </br>

Jawab : </br>

**Pada UML TUBAN** </br>

a. Jalankan perintah `nano /etc/dhcp/dhcpd.conf`</br>

b. Pada `subnet 192.168.0.0 netmask 255.255.255.0` edit `range` menjad: </br>

```
range 192.168.0.10 192.168.0.100;
range 192.168.0.110 192.168.0.200;
```

Sehingga akan terlihat seperti berikut:

![No 2 config DHCP Server](/img/2-config-tuban-1.png)

```
subnet 192.168.0.0 netmask 255.255.255.0 {  #adalah subnet network router dan client# 
  range 192.168.0.10 192.168.0.100;
  range 192.168.0.110 192.168.0.200;
  option routers 192.168.0.1;
  option broadcast-address 192.168.0.255;
  option domain-name-servers 10.151.79.18, 202.46.129.2;
  default-lease-time 300;
  max-lease-time 3000;
}
```

</br>

#### Nomor 4
**4. Client pada subnet 3 mendapatkan range IP dari 192.168.1.50 sampai 192.168.1.70.**  </br>

Jawab : </br>

**Pada UML TUBAN** </br>

a. Jalankan perintah `nano /etc/dhcp/dhcpd.conf`</br>

b. Pada `subnet 192.168.1.0 netmask 255.255.255.0` edit `range` menjad: </br>

```
range 192.168.1.50 192.168.1.70;
```

Sehingga akan terlihat seperti berikut:

![No 2 config DHCP Server](/img/2-config-tuban-2.png)

```
subnet 192.168.1.0 netmask 255.255.255.0 {  #adalah subnet network router dan client# 
  range 192.168.1.50 192.168.1.70;
  option routers 192.168.1.1;
  option broadcast-address 192.168.1.255;
  option domain-name-servers 10.151.79.18, 202.46.129.2;
  default-lease-time 600;
  max-lease-time 6000;
}
```

</br>

#### Nomor 5
**5. Client mendapatkan DNS Malang dan DNS 202.46.129.2 dari DHCP**  </br>

Jawab : </br>

**Pada UML TUBAN** </br>

a. Jalankan perintah `nano /etc/dhcp/dhcpd.conf`. Pada setiap **subnet** edit `option domain-name-servers`. </br>

b. Sehingga sintaks menjadi seperti berikut</br>

```
option domain-name-servers 10.151.79.18, 202.46.129.2;
```

</br>

#### Nomor 6
**6. Client di subnet 1 mendapatkan peminjaman alamat IP selama 5 menit, sedangkan client pada subnet 3 mendapatkan peminjaman IP selama 10 menit.**  </br>

Jawab : </br>

**Pada UML TUBAN** </br>

a. Jalankan perintah `nano /etc/dhcp/dhcpd.conf`</br>

b. Pada **Subnet 1** tambahkan</br>

```
default-lease-time 300;
max-lease-time 3000;
```

</br>

b. Pada **Subnet 2** tambahkan</br>

```
default-lease-time 600;
  max-lease-time 6000;
```

</br>

c. Sehingga menjadi seperti berikut</br>

```
#subnet1 5menit
subnet 192.168.0.0 netmask 255.255.255.0 {  #adalah subnet network router dan client# 
  range 192.168.0.10 192.168.0.100;
  range 192.168.0.110 192.168.0.200;
  option routers 192.168.0.1;
  option broadcast-address 192.168.0.255;
  option domain-name-servers 10.151.79.18, 202.46.129.2;
  default-lease-time 300;
  max-lease-time 3000;
}

#subnet3 10menit
subnet 192.168.1.0 netmask 255.255.255.0 {  #adalah subnet network router dan client# 
  range 192.168.1.50 192.168.1.70;
  option routers 192.168.1.1;
  option broadcast-address 192.168.1.255;
  option domain-name-servers 10.151.79.18, 202.46.129.2;
  default-lease-time 600;
  max-lease-time 6000;
}
```

</br>

## Proxy Server

#### Nomor 7
**7. Menambahkan user autentifikasi, user autentikasi milik Anri memiliki format:**  </br>

| User        | Password          |    
| :-:         | :-:               |
| userta_yyy  | inipassw0rdta_yyy |

</br>

Jawab : </br>

**Pada UML MOJOKERTO** </br>

a. Jalankan perintah `apt-get install apache2-utils` </br>

b. Jalankan perintah `htpasswd -c /etc/squid/passwd userta_d01` </br>

c. Masukkan password `inipassw0rdta_d01` </br>

d. Tambahkan konfigurasi dengan menjalankan perintah `nano /etc/squid/squid.conf`</br>

```
acl all src 0.0.0.0/0.0.0.0
http_port 8080
visible_hostname mojokerto

auth_param basic program /usr/lib/squid/ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED

http_access deny all

http_access allow USERS JADWALDEL !REDIRECT
http_access allow USERS JADWALSEM !REDIRECT
http_access allow USERS JADWALSEMM !REDIRECT
```

</br>

![No 7 Config 1](/img/7-config-1.png)

![No 7 Config 2](/img/7-config-2.png)

e. Jalankan perintah `service squid restart` </br>
f. Nyalakan proxy di setting windows </br>

![No 7 Nyalain Proxy](/img/onProxy.PNG)

g. Testing </br>

![No 7 Testing](/img/no7.PNG)


</br>

#### Nomor 8
**8. Setiap hari Selasa-Rabu pukul 13.00-18.00. Bu Meguri membatasi penggunaan internet Anri hanya pada jadwal yang telah ditentukan itu saja. Maka diluar jam tersebut, Anri tidak dapat mengakses jaringan internet dengan proxy tersebut.** </br>

Jawab : </br>

**Pada UML MOJOKERTO** </br>

a. Tambahkan konfigurasi dengan menjalankan perintah `nano /etc/squid/acl.conf` </br>

```
acl JADWALDEL time TW 13:00-18:00
```

</br>

![No 8 acl](/img/acl-config.png)

b. Tambahkan konfigurasi dengan menjalankan perintah `nano /etc/squid/squid.conf` </br>

```
include /etc/squid/acl.conf
http_access allow USERS JADWALDEL !REDIRECT # konfigurasi sudah ditambahkan pada nomor 7
```

</br>

![No 8 config](/img/8-config.png)

c. Jalankan perintah `service squid restart` </br>

d. Nyalakan proxy di setting windows </br>

![No 8 Nyalain Proxy](/img/onProxy.PNG)

e. Testing </br>

- Cek Waktu Server

![No 8a Testing](/img/no8a.PNG)


![No 8 Testing](/img/no8.PNG)


#### Nomor 9
**9. Jadwal bimbingan dengan Bu Meguri adalah setiap hari Selasa-Kamis pukul 21.00 - 09.00 keesokan harinya (sampai Jumat jam 09.00).** </br>

Jawab : </br>

**Pada UML MOJOKERTO** </br>

a. Tambahkan konfigurasi dengan menjalankan perintah `nano /etc/squid/acl.conf` </br>

```
acl JADWALSEM time TWH 21:00-23:59
acl JADWALSEMM time WHF 00:00-09:00
```

</br>

![No 9 acl](/img/acl-config.png)

b. Tambahkan konfigurasi dengan menjalankan perintah `nano /etc/squid/squid.conf` </br>

```
http_access allow USERS JADWALSEM !REDIRECT  # konfigurasi sudah ditambahkan pada nomor 7
http_access allow USERS JADWALSEMM !REDIRECT # konfigurasi sudah ditambahkan pada nomor 7
```

</br>

![No 9 config](/img/9-config.png)

c. Jalankan perintah `service squid restart` </br>

d. Nyalakan proxy di setting windows </br>

![No 9 Nyalain Proxy](/img/onProxy.PNG)


e. Testing </br>

- Cek Waktu Server

![No 9a Testing](/img/no9a.PNG)


![No 9 Testing](/img/no9.PNG)


#### Nomor 10
**10. Agar Anri bisa fokus mengerjakan TA, setiap dia mengakses google.com, maka akan di redirect menuju monta.if.its.ac.id agar Anri selalu ingat untuk mengerjakan TA** </br>

Jawab : </br>

**Pada UML MOJOKERTO** </br>

a. Tambahkan konfigurasi dengan menjalankan perintah `nano /etc/squid/ban.acl` </br>

```
google.com
```

</br>

![No 10 ban.acl](/img/10-acl.png)

b. Tambahkan konfigurasi dengan menjalankan perintah `nano /etc/squid/squid.conf` </br>

```
acl REDIRECT url_regex "/etc/squid/ban.acl"
deny_info http://monta.if.its.ac.id/ REDIRECT
```

</br>

![No 10 config](/img/10-config.png)

c. Jalankan perintah `service squid restart` </br>

d. Nyalakan proxy di setting windows </br>

![No 10 Nyalain Proxy](/img/onProxy.PNG)

e. Testing </br>

![No 10 Testing](/img/no10.PNG)


#### Nomor 11
**11. Untuk menandakan bahwa Proxy Server ini adalah Proxy yang dibuat oleh Anri, (11) Bu Meguri meminta Anri untuk mengubah error page default squid menjadi seperti berikut:** </br>

Note : File error page bisa diunduh dengan cara wget 10.151.36.202/ERR_ACCESS_DENIED, Tidak perlu di extract, cukup cp -r </br>

Jawab : </br>

**Pada UML MOJOKERTO** </br>

a. Jalankan perintah `mkdir /etc/squid/pages/` </br>

b. Setelah itu download file dengan cara menjalakan perintah `wget 10.151.36.202/ERR_ACCESS_DENIED` </br>

c. Copy file yang didownload masukkan ke directory /etc/squid/pages/ `cp -r /ERR_ACCESS_DENIED /etc/squid/pages/` </br>

d. Tambahkan konfigurasi dengan menjalankan perintah `nano /etc/squid/squid.conf` </br>

```
error_directory /etc/squid/pages/
```

</br>

![No 11 config](/img/11-config.png)

e. Jalankan perintah `service squid restart` </br>

f. Nyalakan proxy di setting windows </br>

![No 11 Nyalain Proxy](/img/onProxy.PNG)

g. Testing </br>

![No 11 Testing](/img/no11.PNG)


#### Nomor 12
**12. Karena Bu Meguri dan Anri adalah tipe orang pelupa, maka untuk memudahkan mereka, Anri memiliki ide ketika menggunakan proxy cukup dengan mengetikkan domain janganlupa-ta.yyy.pw dan memasukkan port 8080.** </br>

Keterangan : yyy adalah nama kelompok masing-masing. Contoh: janganlupa-ta.c01.pw </br>

Jawab : </br>

**Pada UML MALANG** </br>

a. Jalankan perintah `nano /etc/bind/modul3/janganlupa-ta.d01.pw` </br>

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     janganlupa-ta.d01.pw. root.janganlupa-ta.d01.pw. (
                         11242020       ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      janganlupa-ta.d01.pw.
@       IN      A       10.151.79.19
```

</br>

![No 12](/img/no12-1.png)

b. jalankan perintah `nano /etc/bind/named.conf.local` </br>

```
//
// Do any local configuration here
// 

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "janganlupa-ta.d01.pw"{
        type master;
        file "/etc/bind/modul3/janganlupa-ta.d01.pw";
}
```

</br>

![No 12](/img/no12-2.png)

c. jalankan perintah `service bind9 restart` </br>

d. Nyalakan proxy di setting windows </br>

![No 12 Nyalain Proxy](/img/newProxy.PNG)

e. Testing </br>

![No 12 Testing](/img/no12a.PNG)
