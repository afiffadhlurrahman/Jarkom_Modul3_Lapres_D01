# Modul 3 : DHCP dan Proxy Server
Nama Anggota :

- 05111840000093 Muhammad Afif Fadhlurrahman
- 05111740000091 Affan Ahsanul Habib

Session :
- [DHCP](#dhcp)
- [Proxy Server](#proxy-server)
   * [No 7](#nomor-7)
   * [No 8](#nomor-8)
   * [No 9](#nomor-9)
   * [No 10](#nomor-10)
   * [No 11](#nomor-11)
   * [No 12](#nomor-12)

## DHCP

**1. Membuat Topologi** </br>

**2. SURABAYA ditunjuk sebagai perantara (DHCP Relay) antara DHCP Server dan client.**  </br>

**3. Client pada subnet 1 mendapatkan range IP dari 192.168.0.10 sampai 192.168.0.100 dan 192.168.0.110 sampai 192.168.0.200.**  </br>

**4. Client pada subnet 3 mendapatkan range IP dari 192.168.1.50 sampai 192.168.1.70.**  </br>

**5. Client mendapatkan DNS Malang dan DNS 202.46.129.2 dari DHCP**  </br>

**6. Client di subnet 1 mendapatkan peminjaman alamat IP selama 5 menit, sedangkan client pada subnet 3 mendapatkan peminjaman IP selama 10 menit.**  </br>

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

b. Tambahkan konfigurasi dengan menjalankan perintah `nano /etc/squid/squid.conf` </br>

```
include /etc/squid/acl.conf
http_access allow USERS JADWALDEL !REDIRECT # konfigurasi sudah ditambahkan pada nomor 7
```

</br>

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

b. Tambahkan konfigurasi dengan menjalankan perintah `nano /etc/squid/squid.conf` </br>

```
http_access allow USERS JADWALSEM !REDIRECT  # konfigurasi sudah ditambahkan pada nomor 7
http_access allow USERS JADWALSEMM !REDIRECT # konfigurasi sudah ditambahkan pada nomor 7
```

</br>

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

b. Tambahkan konfigurasi dengan menjalankan perintah `nano /etc/squid/squid.conf` </br>

```
acl REDIRECT url_regex "/etc/squid/ban.acl"
deny_info http://monta.if.its.ac.id/ REDIRECT
```

</br>

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

c. jalankan perintah `service bind9 restart` </br>

d. Nyalakan proxy di setting windows </br>

![No 12 Nyalain Proxy](/img/newProxy.PNG)

e. Testing </br>

![No 12 Testing](/img/no12a.PNG)
