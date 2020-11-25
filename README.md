# Modul 3 : DHCP dan Proxy Server
Nama Anggota :

- 05111840000093 Muhammad Afif Fadhlurrahman
- 05111740000091 Affan Ahsanul Habib

Link :
[DHCP](#dhcp)
[Proxy Server](#proxy-server)

### DHCP

1. Membuat Topologi </br>

2. SURABAYA ditunjuk sebagai perantara (DHCP Relay) antara DHCP Server dan client. </br>

3. Client pada subnet 1 mendapatkan range IP dari 192.168.0.10 sampai 192.168.0.100 dan 192.168.0.110 sampai 192.168.0.200. </br>

4. Client pada subnet 3 mendapatkan range IP dari 192.168.1.50 sampai 192.168.1.70. </br>

5. Client mendapatkan DNS Malang dan DNS 202.46.129.2 dari DHCP </br>

6. Client di subnet 1 mendapatkan peminjaman alamat IP selama 5 menit, sedangkan client pada subnet 3 mendapatkan peminjaman IP selama 10 menit. </br>

### Proxy Server

7. Menambahkan user autentifikasi, user autentikasi milik Anri memiliki format: </br>

| User        | Password          |    
| :-:         | :-:               |
| userta_yyy  | inipassw0rdta_yyy |
</br>

8. Setiap hari Selasa-Rabu pukul 13.00-18.00. Bu Meguri membatasi penggunaan internet Anri hanya pada jadwal yang telah ditentukan itu saja. Maka diluar jam tersebut, Anri tidak dapat mengakses jaringan internet dengan proxy tersebut. </br>

9. Jadwal bimbingan dengan Bu Meguri adalah setiap hari Selasa-Kamis pukul 21.00 - 09.00 keesokan harinya (sampai Jumat jam 09.00). </br>

10. Agar Anri bisa fokus mengerjakan TA, setiap dia mengakses google.com, maka akan di redirect menuju monta.if.its.ac.id agar Anri selalu ingat untuk mengerjakan TA </br>

11. Untuk menandakan bahwa Proxy Server ini adalah Proxy yang dibuat oleh Anri, (11) Bu Meguri meminta Anri untuk mengubah error page default squid menjadi seperti berikut: </br>
```
Note : File error page bisa diunduh dengan cara wget 10.151.36.202/ERR_ACCESS_DENIED
   Tidak perlu di extract, cukup cp -r
```
</br>

12. Karena Bu Meguri dan Anri adalah tipe orang pelupa, maka untuk memudahkan mereka, Anri memiliki ide ketika menggunakan proxy cukup dengan mengetikkan domain janganlupa-ta.yyy.pw dan memasukkan port 8080.
```
Keterangan : yyy adalah nama kelompok masing-masing. 
Contoh: janganlupa-ta.c01.pw
```
