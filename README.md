# Jarkom-Modul-3-A10-2021

## Topologi

![image](https://user-images.githubusercontent.com/74232912/141284231-e747820a-4d76-44f3-a9aa-203e8851e9e5.png)


### 1. Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server
#### EniesLobby sebagai DNS Server, maka di EniesLobby diinstall Bind9

```
apt-get update
apt-get install bind9 -y
```

![image](https://user-images.githubusercontent.com/74232912/141284592-b9185590-fcf6-4a57-ac05-0505ba3ecbc1.png)


#### Jipangu sebagai DHCP Server, maka pada jipangu diinstall isc-dhcp-server

```
apt-get update
apt-get install isc-dhcp-server -y
```

![image](https://user-images.githubusercontent.com/74232912/141284858-27edef5e-bbfb-497f-8696-7dbe599f3b8d.png)


#### Water7 sebagai Proxy Server, maka pada Water7 diinstall squid

```
apt-get update
apt-get install squid -y
```

![image](https://user-images.githubusercontent.com/74232912/141285164-2f546d5a-0709-4f65-9e56-468a355df2c8.png)

Setelah Proxy Server diinstall, maka check status nya dengan

```
service squid status
```

![image](https://user-images.githubusercontent.com/74232912/141290882-4a313ac0-44fd-437b-84d9-aaa9e277a24c.png)

Jika, status squid belum running. Maka lakukan command ini terlebih dahulu

```
service squid start
```

Setelah itu, check kembali status dan squid akan running


### 2.  Foosha sebagai DHCP Relay
#### Foosha sebagai DHCP Relay atau router, maka diinstall isc-dhcp-relay

```
apt-get update
apt-get install isc-dhcp-relay -y
```

![image](https://user-images.githubusercontent.com/74232912/141285493-00655580-73fc-4df2-937c-a74494cbacf1.png)

#### Setelah install maka akan diminta masukkan IP dari server

![image](https://user-images.githubusercontent.com/74232912/141285643-53de24d1-230d-422d-81b7-c28dac1f8d5e.png)

Disini IP server adalah IP nya Jipangu yaitu ``` 10.4.2.4``` karena telah ditetapkan sebagai Server pada No. 1

#### Setelah memasukkan IP server, maka akan dimasukkan Interfaces-nya

![image](https://user-images.githubusercontent.com/74232912/141285918-33eee139-3273-45c3-a290-7dbfd131348e.png)

Disini Interfaces nya dimasukkan ```eth1 eth2 eth3```.

#### Setelah itu, pada bagian "Additional options that are passed to the DHCP relay daemon" boleh dikosongkan

![image](https://user-images.githubusercontent.com/74232912/141286139-e94e10b0-8f16-4ac7-aa1d-bd171c8443df.png)

#### jika ingin melihat hasil tersebut, bisa buka pada Foosha

```
nano /etc/default/isc-dhcp-relay
```

![image](https://user-images.githubusercontent.com/74232912/141286386-ce9403fb-887f-4423-b8ff-4de3ec5f3717.png)


### 3. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169.
#### Pada node Jipangu, buka " etc/dhcp/dhcpd.conf"

```
nano /etc/dhcp/dhcpd.conf
```

![image](https://user-images.githubusercontent.com/74232912/141286640-85950179-32fc-4f32-84df-dbba143fa104.png)

#### Setelah itu tambahkan konfigurasi berikut

```
subnet 10.4.1.0 netmask 255.255.255.0 {
    range 10.4.1.20 10.4.1.99;
    range 10.4.1.150 10.4.1.169;
    option routers 10.4.1.1;
    option broadcast-address 10.4.1.255;

    option domain-name-servers 10.4.2.2;

    default-lease-time 360;
    max-lease-time 7200;
}
```

![a](https://user-images.githubusercontent.com/74232912/141287860-f5021082-f46c-4af1-b83c-9f6a1c6ff8ba.png)


### 4. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50
#### Sama dengan nomor sebelumnya, pada Jipangu buka "/etc/dhcp/dhcpd.conf"
```
nano /etc/dhcp/dhcpd.conf
```

#### Kemudian masukkan konfigurasi berikut

```
subnet 10.4.3.0 netmask 255.255.255.0 {
    range 10.4.3.30 10.4.3.50;
    option routers 10.4.3.1;
    option broadcast-address 10.4.3.255;

    option domain-name-servers 10.4.2.2; 

    default-lease-time 720; 
    max-lease-time 7200; 
}
```

![b](https://user-images.githubusercontent.com/74232912/141287929-f75496fd-3b78-4262-9d41-b0c7c8d152d1.png)


### 5. Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut
#### pada Jipangu "/etc/dhcp/dhcpd.conf" tambahkan konfigurasi seperti pada gambar

![c](https://user-images.githubusercontent.com/74232912/141288130-0152217e-013e-43f7-87ad-87f185caf9cd.png)

Ditambahkan ```10.4.2.2``` sebagai DNS nya yang meripakan IP dari DNS Server yaitu EniesLobby


### 6. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit
#### pada Jipangu "/etc/dhcp/dhcpd.conf" Kemudian ditambahkan pada setiap switch nya

![d](https://user-images.githubusercontent.com/74232912/141288406-8b134f87-b9de-4ec8-8a23-5cba4fc51de7.png)

Melalui switch1 selama 6 menit --> 360 detik
Melalui switch2 selama 12 menit --> 720 detik
Maksimal peminjaman IP 120 menit --> 7200 detik

#### jangan lupa di simpan dan restart

```
service isc-dhcp-server restart
```

![image](https://user-images.githubusercontent.com/74232912/141290577-b1b96a2d-f03a-42e8-90d2-6cd16072e16d.png)

#### dan check statusnya

```
service isc-dhcp-server status
```

![image](https://user-images.githubusercontent.com/74232912/141290658-42183461-85b0-4ee7-8355-a4e6ad2df905.png)

#### Jika status dhcp running. Maka pada node client Alabasta, Loguetown, Skypie, dan Tottoland diubah interfaces nya.

Buka ```/etc/network/interfaces``` pada semua node client dan masukkan konfigurasi berikut

```
auto eth0
iface eth0 inet dhcp
```

![image](https://user-images.githubusercontent.com/74232912/141292242-159bae1a-45af-4a58-967a-75f0a6aa6727.png)

Setelah ditambahkan konfigurasi tersebut, maka restart setiap Node melalui GNS3 nya.
Periksa dengan command

```
cat /etc/resolv.conf
```

Jika sesuai dengan gambar diabawah maka konfigurasi nya berhasil

![image](https://user-images.githubusercontent.com/74232912/141292416-97c7f135-df11-43a3-9d70-142a93b1ccda.png)


### 7. Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69
#### Pada Jipangu "/etc/dhcp/dhcpd.conf" ditambahkan konfigurasi berikut

```
host Skypie {
    hardware ethernet d2:61:cf:ce:81:09;
    fixed-address 10.4.3.69;
}
```

![f](https://user-images.githubusercontent.com/74232912/141289170-76c482b6-6259-4f38-b0c3-cb3834d2053f.png)

yang mana ```hardware ethernet d2:61:cf:ce:81:09``` bisa diliat pada node Skypie dengan menjalankan perintah ```ip a```. Kemudian liat pada bagian eth0, dan liat pada bagian link/ether

![e](https://user-images.githubusercontent.com/74232912/141289047-4b3959be-afd7-45a7-977f-ebcfb08f5cf4.png)

#### Setelah mengisi konfigurasi diatas, maka jangan lupa disimpan dan di restart

#### Setelah itu pada node skypie, buka "/etc/network/interfaces"

```
nano /etc/network/interfaces
```

dan tambahkan konfigurasi berikut

```
hwaddress ether d2:61:cf:ce:81:09
```

![image](https://user-images.githubusercontent.com/74232912/141292799-b8fe3504-20bf-4966-a356-21fc936cc6a8.png)

#### Jika sudah maka restart Node melalui GNS3. Dan IP pada skypie akan berubah menjadi ```10.4.3.69```

![g](https://user-images.githubusercontent.com/74232912/141293053-87d17ca6-84c3-4594-9264-b0c7ca904d7a.png)


### 8. Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000
#### Buatlah Domain jualbelikapal.A10.com pada EniesLobby menggunakan bind9

#### Pada node Water7, buka "/etc/squid/squid.conf"

```
nano /etc/squid/squid.conf
```

kemudian isikan konfigurasi berikut

```
http_port 5000
visible_hostname jualbelikapal.A10.com
http_access allow all
```

Setelah itu, simpan dan restart squid

```
service squid restart
```

#### Kemudian pada Loguetown, aktifkan Proxynya dengan

```
export http_proxy="http://10.4.2.3:5000"
```

di cek proxynya dengan command

```
env | grep -i 
```

#### Setelah di hidupkan proxynya, akses jualbelikapal.A10.com di Loguetown


### 9. Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy
#### Pada node Water7 dijalankan perintah berikut

```
htpasswd -m /etc/squid/passwd luffybelikapalA10
```



### 10. Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00)


### 11. Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie


### 12. Saatnya berlayar! Luffy dan Zoro akhirnya memutuskan untuk berlayar untuk mencari harta karun di super.franky.yyy.com. Tugas pencarian dibagi menjadi dua misi, Luffy bertugas untuk mendapatkan gambar (.png, .jpg), sedangkan Zoro mendapatkan sisanya. Karena Luffy orangnya sangat teliti untuk mencari harta karun, ketika ia berhasil mendapatkan gambar, ia mendapatkan gambar dan melihatnya dengan kecepatan 10 kbps 


### 13. Sedangkan, Zoro yang sangat bersemangat untuk mencari harta karun, sehingga kecepatan kapal Zoro tidak dibatasi ketika sudah mendapatkan harta yang diinginkannya
