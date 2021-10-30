# Laporan Praktikum Modul 1

## Anggota  Kelompok D15 :

## Richard Asmarakandi (05111940000017)

## Nurul Izzatil Ulum (05111940000058)



## SOAL

### 1. EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet

* yang penting untuk dilakukanadalah, menulis `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.199.0.0/16` di router `Foosha` agar lalu lintas data dapat berjalan.
setelah itu ketikkan `cat /etc/resolv.conf` dan ganti namaserver menjadi `nameserver 192.168.122.1` agar dapat terhubung ke internet. ketikkan `echo nameserver 192.168.122.1 > /etc/resolv.conf` di node yang lain dan isi nameserver dengan IP Foosha yang telah kita ganti, ini dilakukan agar node yang lain dapat mengakses internet juga.
* cara mengecek apakah sudah terhubung apap tidak adalah denagn men-ping google pada semua node.
* (gambar)


### 2. Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku

* Lakukan perintah pada EniesLobby. Isikan seperti berikut: `nano /etc/bind/named.conf.local`

* Isikan configurasi domain franky.d15.com sesuai dengan syntax berikut:
``zone "franky.d15.com" {
	type master;
	file "/etc/bind/jarkom/franky.d15.com";
};``
(gambar)

* Buka folder kaizoku di dalam `/etc/bind` : `mkdir /etc/bind/kaizoku`

* Copykan file db.local pada path /etc/bind ke dalam folder kaizoku yang baru saja dibuka dari zip dan ubah namanya menjadi franky.d15.com `cp /etc/bind/db.local /etc/bind/kaizoku/franky.d15.com`

* Kemudian buka file franky.d15.com dan edit seperti gambar berikut dengan IP EniesLobby masing-masing kelompok: `vim /etc/bind/jarkom/franky.d15.com`
(gambar)

* untuk membuat CNAME, Buka file franky.d15.com pada server EniesLobby dan tambahkan konfigurasi seperti pada gambar berikut:
(gambar)

* Restart bind9 dengan perintah `service bind9 restart`


### 3. Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie

* Edit file `/etc/bind/jarkom/franky.d15.com` lalu tambahkan subdomain untuk `super.franky.d15.com` yang mengarah ke IP Water7. `vim /etc/bind/jarkom/jarkom2021.com`

* Tambahkan konfigurasi seperti pada gambar ke dalam file `franky.d15.com.`
(gambar)

* Restart bind9 dengan perintah `service bind9 restart`

* ping ke subdomain dengan perintah berikut dari client Loguetown

``ping super.franky.d15.com
ATAU
host -t A super.franky.d15.com``
(gambar)


### 4. Buat juga reverse domain untuk domain utama 
* Mengubah isi dari file /etc/bind/named.conf.local pada server EniesLobby dan menambahkan konfigurasi seperti berikut : `vim /etc/bind/named.conf.local`
Tambahkan reverse dari 3 byte awal dari IP yang ingin dilakukan Reverse DNS. Karena di contoh menggunakan IP 10.40.2 untuk IP dari records, maka reversenya adalah 2.40.10

``zone "2.40.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.40.10.in-addr.arpa";
};``
(gambar halaman conf nya)

* Copykan file db.local pada path /etc/bind ke dalam folder shiftmodul2 yang baru saja dibuat dan ubah namanya menjadi 2.40.10.in-addr.arpa
`cp /etc/bind/db.local /etc/bind/jarkom/2.40.10.in-addr.arpa`

* Edit file 77.151.10.in-addr.arpa seperti berikut 
(gambar)

* Kemudian restart bind9 dengan perintah `service bind9 restart`

* Untuk mengecek apakah konfigurasi sudah benar atau belum, lakukan perintah berikut pada client Loguetown
`host -t PTR "IP EniesLobby`


### 5. Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama

#### Konfigurasi pada EniesLobby
* Edit file /etc/bind/named.conf.local dan sesuaikan dengan syntax berikut

`zone "franky.d15.com" {
    type master;
    notify yes;
    also-notify { "IP Water7"; }; // Masukan IP Water7 tanpa tanda petik
    allow-transfer { "IP Water7"; }; // Masukan IP Water7 tanpa tanda petik
    file "/etc/bind/jarkom/franky.d15.com";
};`
(gambar)

* Lakukan restart bind9 `service bind9 restart`

#### Konfigurasi pada Water7
* Buka Water7 dan update package lists dengan menjalankan command: `apt-get update`

* install aplikasi bind9 pada Water7 dengan `apt-get install bind9 -y`

* Kemudian buka file `/etc/bind/named.conf.local` pada Water7 dan tambahkan syntax berikut:

``zone "franky.d15.com" {
    type slave;
    masters { "IP EniesLobby"; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/franky.d15.com";
};``
(gambar)

* Lakukan restart bind9 `service bind9 restart`

### 6. Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo

#### Konfigurasi pada EniesLobby
* Pada EniesLobby, edit file /etc/bind/jarkom/franky.d15.com.com dan ubah menjadi seperti di bawah ini sesuai dengan pembagian IP EniesLobby kelompok masing-masing.
`vim /etc/bind/jarkom/franky.d15.com`
(gambar)

* Kemudian edit file /etc/bind/named.conf.options pada EniesLobby.
`vim /etc/bind/named.conf.options`
(gambar)

* Kemudian comment dnssec-validation auto; dan tambahkan baris berikut pada /etc/bind/named.conf.options `allow-query{any;};`
(gambar)

* Kemudian edit file /etc/bind/named.conf.local menjadi seperti gambar di bawah:

``zone "franky.d15.com" {
    type master;
    file "/etc/bind/jarkom/franky.d15.com";
    allow-transfer { "IP Water7"; }; // Masukan IP Water7 tanpa tanda petik
};``
(gambar)

* Setelah itu restart bind9 `service bind9 restart`


#### Konfigurasi pada Water7
* Pada Water7 edit file /etc/bind/named.conf.options `vim /etc/bind/named.conf.options`

* Kemudian comment dnssec-validation auto; dan tambahkan baris berikut pada /etc/bind/named.conf.options `allow-query{any;};`
* Lalu edit file /etc/bind/named.conf.local menjadi seperti gambar di bawah:
(gamabr)

* Kemudian buat direktori dengan nama delegasi, Copy db.local ke direktori delegasi dan edit namanya menjadi mecha.franky.d15.com
``mkdir /etc/bind/delegasi
cp /etc/bind/db.local /etc/bind/delegasi/its.jarkom2021.com``

* Kemudian edit file mecha.franky.d15.com menjadi seperti dibawah ini
(gamabr)

* Restart bind9 `service bind9 restart`


### 7. Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Franky dengan nama general.mecha.frank.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie

#### Konfigurasi di server EniesLobby

* Mengubah isi dari file /etc/bind/delegasi/general.mecha.frank.d15.com seperti gambar berikut :
(gambar)

* Lakukan restart service bind `service bind9 restart`

* Coba ping di server Loguetown ping general.mecha.frank.d15.com
(gambar)


### 8. Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com.

#### Konfigurasi Server Skypie

* Install apache pada uml Skypie dengan command : `apt-get install apache2`

* Install php pada uml Skypie dengan command : `apt-get install php`

* Pindah ke direktori /etc/apache2/sites-available dan copy file 000-default.conf dan rename dengan nama franky.d15.com.
`cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/franky.d15.com.conf`
(gambar)

* Edit file franky.d15.com.conf
(gambar)

* aktifkan dengan command `a2ensite franky.d15.com.`

* Pindah ke direktori /var/www/

* Download file website dengan cara `wget [IP]/franky.com.zip`

* Unzip file yang sudah didownload dan ganti nama file
(gambar)

* Restart apache : `service apache2 restart`

* Ketika franky.d15.com. diakses, akan menampilkan seperti di gambar berikut
(gambar)


### 9. Setelah itu, Luffy juga membutuhkan agar url www.franky.yyy.com/index.php/home dapat menjadi menjadi www.franky.yyy.com/home.

* Untuk melakukannya, kita dapat menggunakan direktori alias. Pada konfigurasi di folder /etc/apache2/sites-available/franky.d15.com.conf kita tambahkan line baru dan menulis `Alias "/home" "/var/www/franky.d15.com/home.html"`. 

![9-A](https://cdn.discordapp.com/attachments/804405775988555776/903846951760580728/Screenshot_7.png)

* Nantinya secara otomatis ketika kita mengetikkan super.franky.d15.com/home maka akan diarahkan untuk membuka file `home.html` yang berada pada `/var/www/franky.d15.com/home.html` untuk megeceknya kita gunakan command `lynx franky.d15.com/home` pada Loguetown. Jika berhasil akan tampil seperti berikut
![9-B](https://cdn.discordapp.com/attachments/804405775988555776/903850393845510205/Untitled.png)

### 10. Setelah itu, pada subdomain www.super.franky.yyy.com, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/super.franky.yyy.com .

* Untuk melakukannya, maka kita perlu membuat konfigurasi bari untuk subdomain `super.franky.d15.com`. maka kita buat konfigurasinya dengan membuat file baru bernama `super.franky.d15.com.conf` pada `/etc/apache2/sites-available/`. Isi file akan seperti ini.

![10-A](https://cdn.discordapp.com/attachments/804405775988555776/903849831498412102/Untitled.png)

* Setelah itu kita aktifkan konfigurasinya menggunakan `a2ensite super.franky.d15.com`, setelah itu restart apache2. Untuk memastikannya kita cek dengan menggunakan `lynx super.franky.d15.com` pada loguetown, maka akan muncul tampilan seperti berikut

![10-B](https://cdn.discordapp.com/attachments/804405775988555776/903857571234992168/Untitled.png)

### 11. Akan tetapi, pada folder /public, Luffy ingin hanya dapat melakukan directory listing saja.

* Untuk melakukannya kita perlu membloki akses pada folder `error` agar tidak dapat diakses oleh orang lain atau host lain. maka kita gunakan directory listing. kita edit konfigurasi `/etc/apache2/sites-available/super.franky.d15.com.conf`. Lalu tambahkan konfigurasi menjadi seperti ini

![11-A](https://cdn.discordapp.com/attachments/804405775988555776/903858195070582814/unknown.png)

* Maka saat kita membuka web dengan `lynx super.franky.d15.com` dan membuka file `error` di dalamnya, maka akan ada error `403 forbidden` seperti berikut

![11-B](https://cdn.discordapp.com/attachments/804405775988555776/903859028256497674/unknown.png)

### 12. Tidak hanya itu, Luffy juga menyiapkan error file 404.html pada folder /errors untuk mengganti error kode pada apache.

* Untuk mengganti error, kita tambahkan `ErrorDocument 404 /var/www/super.franky.d15.com/error/404.html` pada konfigurasi `/etc/apache2/sites-available/super.franky.d15.com.conf`.

![12-A](https://cdn.discordapp.com/attachments/804405775988555776/903859596521775144/unknown.png)

* Maka untuk mengeceknya, kita coba masukkan command `lynx super.franky.d15.com/terserah`. Ganti `terserah` dengan kata apapun yang tidak ada pada folder `/var/www/super.franky.d15.com` agar muncul error 404 not found. Jika berhasil maka akan muncul tampilan seperti berikut

![12-B](https://cdn.discordapp.com/attachments/804405775988555776/903860099230093332/unknown.png)

### 13. Luffy juga meminta Nami untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.super.franky.yyy.com/public/js menjadi www.super.franky.yyy.com/js.

* Untuk melakukannya sama seperti soal no 9 menggunakan direktori alias. Tambahkan baris `Alias "/js" "/var/www/super.franky.d15.com/public/js"` pada konfigurasi pada `/etc/apache2/sites-available/super.franky.d15.com.conf`.

![13-A](https://cdn.discordapp.com/attachments/804405775988555776/903860767957352448/unknown.png)

* Untuk mencobanya kita masukkan command `lynx super.franky.d15.com/js` pada Loguetown. Maka akan muncul tampilan sebagai berikut

![13-B](https://cdn.discordapp.com/attachments/804405775988555776/903861215862874133/unknown.png)

### 14. Dan Luffy meminta untuk web www.general.mecha.franky.yyy.com hanya bisa diakses dengan port 15000 dan port 15500

* Untuk melakukanya kita buat konfigurasi untuk `general.mecha.franky.d15.com` dengan membuat filenya. Dapat dilakukan dengan command `vim /etc/apache2/sites-available/general.mecha.franky.d15.com.conf`. Lalu berikut konfigurasinya agar `general.mecha.franky.d15.com` hanya bisa diakses dengan port 15000 155000.

![14-A](https://cdn.discordapp.com/attachments/804405775988555776/903861819305779201/unknown.png)

* Lalu kita ubah settingan pada `/etc/apache2/ports.conf` agar bisa mendengarkan port baru yaitu 15000 dan 15500 dengan cara menambahkan beberapa baris konfigurasi yaitu `Listen 15500` dan `Listen 15000` seperti berikut

![14-B](https://cdn.discordapp.com/attachments/695620854465167360/903868563461406771/unknown.png)

* Untuk mengeceknya, gunakan command `lynx general.mecha.franky.d15.com:15000`. Jika muncul tampilan seperti ini berarti konfigurasi telah berhasil.

![14-C](https://cdn.discordapp.com/attachments/804405775988555776/903871327713243146/unknown.png)

### 15. dengan authentikasi username luffy dan password onepiece dan file di /var/www/general.mecha.franky.yyy

* Untuk menggunakan autentikasi pada apache2, kami mencari referensi dan akhirya menemukan website [ini](https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-apache-on-ubuntu-14-04). Singkatnya, kita tambahkan beberapa konfigurasi pada `/etc/apache2/sites-available/general.mecha.franky.d15.com.conf` menjadi seperti berikut

![15-A](https://cdn.discordapp.com/attachments/804405775988555776/903872410317643846/unknown.png)

* Lalu jangan lupa kita meng-generate sekaligus membuat file pada `/var/www/general.mecha.franky.d15.com` dengan nama `.htpasswd` sesuai konfigurasi dan digunakan untuk menyimpan username dan password sesuai perintah soal. cara membuatnya dengan melakukan command `htpasswd -b -c .htpasswd luffy onepiece` pada folder `/var/www/general.mecha.franky.d15.com/`. Jika berhasil, akan ada output command bertuliskan `Adding password for user luffy` seperti berikut

![15-B](https://media.discordapp.net/attachments/804405775988555776/903873489533038642/unknown.png?width=842&height=473)

* Karena sudah kita beri autentikasi, maka seharusnya ketika kita mengetikkan `lynx general.mecha.franky.d15.com` akan diminta username dan password untuk autentikasi seperti pada gambar berikut, dan jika kita masukkan `luffy` sebagai username serta `onepiece` sebagai passwordnya, maka otomatis halaman akan terbuka

![15-C](https://cdn.discordapp.com/attachments/804405775988555776/903876834851180654/unknown.png)

### 16. Dan setiap kali mengakses IP EniesLobby akan diahlikan secara otomatis ke www.franky.yyy.com

* Untuk melakukannya, kita ubah konfigurasi pada `/etc/apache2/sites-available/000-default.conf` dan mengubah document root ke `var/www/franky.d15.com/` seperti berikut.

![16-A](https://cdn.discordapp.com/attachments/804405775988555776/903878823844667442/unknown.png)

* Untuk mengeceknya kita masukkan command `lynx 192.199.2.4`. `192.199.2.4` merupakan IP Skypie milik kelompok kami. maka akan dialihkan ke halaman franky.d15.com.

![16-B](https://cdn.discordapp.com/attachments/804405775988555776/903879980642095154/unknown.png)

### 17. Dikarenakan Franky juga ingin mengajak temannya untuk dapat menghubunginya melalui website www.super.franky.yyy.com, dan dikarenakan pengunjung web server pasti akan bingung dengan randomnya images yang ada, maka Franky juga meminta untuk mengganti request gambar yang memiliki substring “franky” akan diarahkan menuju franky.png.

* Untuk melakukannya, kita gunakan Module rewrite seperti pada modul. Pertama-tama kita tuliskan command `a2enmod rewrite` untuk mengaktifkan modul rewrite. Lalu kita restart apachenya. Setelah itu kita tambahkan konfigurasi pada `/etc/apache2/sites-available/super.franky.d15.com.conf` menjadi seperti berikut

![17-A](https://cdn.discordapp.com/attachments/804405775988555776/903880862205116536/unknown.png)

* Penjelasan :
Kita gunakan regex `(?=.*franky.*)` untuk mendeteksi berbagai file yang memiliki substring `franky` dan `(^((?!franky\.png).)*$)` untuk mendeteksi agar `franky.png` tidak perlu diubah. Karena apabila regex tidak mendeteksi `franky.png`, maka akan terus dikonversi ke `franky.png` dan menjadi infinite loop. Setelah kondisinya terpenuhi, maka ganti semua url yaitu `.` menjadi `http://super.franky.d15.com/public/images/franky.png`.

* Setelah itu restart apache dan kita coba ketikkan command `lynx super.franky.d15.com/public/images/bukanfrankytapirandom.99689` pada Loguetown. Apabila muncul tampilan seperti berikut, maka konfigurasi telah berhasil

![17-B](https://cdn.discordapp.com/attachments/804405775988555776/903884178540609646/unknown.png)

![17-C](https://cdn.discordapp.com/attachments/804405775988555776/903884399857242112/unknown.png)
