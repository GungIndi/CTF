# WRITE UP GEMASTIK XV

## Forensic

**1. Traffic Enjoyer**

Untuk mendapat flag dari challenge ini kami menggunakan analisa pada traffic dalam file `traffic.pcap` menggunakan `wireshark` lalu menggunakan `cyberchef` untuk mendecode kode `base64` di dalam traffic dan menggunakan online base64 to image converter untuk menemukan bagian dari flag yang akan disusun lagi secara berurutan.

**Hasil Analisa Traffic**

![Traffic](/GEMASTIK_XV/img/traffic.png)

Saat menganalisa traffic, kami menyadari bahwa dalam traffic ada
pesan yang dienkripsi dengan `base64` pada setiap traffic yang
ada. dan setelah di decode, ternyata data yang dikirim adalah sebuah file png yang bisa dikenali dengan header `.png`

**Decode base64**

![Decode](/GEMASTIK_XV/img/decode.png)

Setelah tahu jika didalamnya terdapat file png, kami menggunakan
online tools untuk convert `base64` menjadi gambar, dan ternyata
semua gambar yang diberikan berupa angka,huruf atau karakter
tertentu. Dimana karakter tersebut merupakan bagian dari flag.

**Convert base64 to PNG**

![convert](/GEMASTIK_XV/img/convertbase.png)

**Merangkai flag**

![arrange](/GEMASTIK_XV/img/arrangeflag1.png)

![arrange](/GEMASTIK_XV/img/arrangeflag2.png)

kami mendapat huruf yang merupakan bagian dari flag yang acak,
setelah kami menemukan huruf G besar yang merupakan awal dari
format sebuah flag dan pesan tersebut ada di `index = 0`, maka
kami mengurutkan semua karakter yang kami dapat sesuai pada
index pesannya masing masing dan menjadi sebuah flag.

**Flag**

***Gemastik2022{balapan_f1rst_bl00d_is_real_f580c176}***

<br>

## Reverse Engineering

**2. CodeJugling**

Untuk mendapatkan flag dari challenge ini kami melakukan dekompilasi pada file reversing-itu-mudah dengan menggunakan perangkat lunak `Ghidra`.

**Hasil Dekompilasi**

![Dekompilasi](/GEMASTIK_XV/img/dekompilasi.png)

Gambar diatas adalah hasil dekompilasi dari file `reversing-itu-mudah` dengan menggunakan perangkat lunak `Ghidra`. Untuk masuk kefungsi utama (main) dapat dilakukan dengan klik dua kali pada `FUN_00401140`.

**Fungsi Main**

![main](/GEMASTIK_XV/img/main.png)

Gambar diatas adalah hasil dekompilasi dari fungsi utama (main) dari file `reversing-itu-mudah`. Pada pada bagian statement atau pernyataan yang mensyaratkan kondisi `param1` sama dengan dua,terdapat beberapa fungsi berbeda. Jika fungsi-fungsi tersebut dibuka maka akan didapatkan sebuah karakter yang jika disusun akan membentuk string dari flag yang dicari.

![dekom](/GEMASTIK_XV/img/dekom.png)

Gambar diatas adalah hasil dekompilasi dari fungsi-fungsi yang terdapat pada bagian statement atau pernyataan yang mensyaratkan kondisi `param1` sama dengan dua di fungsi utama sebelumnya. Dapat dilihat terdapat karakter ‘G’ yang merupakan karakter penyusun dari string flag yang dicari. Untuk mendapatkan karakter penyusun lainnya dapat dilakukan dengan cara yang sama pada fungsi-fungsi lainnya.

**Flag**

***Gemastik2022{st45iUn_MLG_k07a_b4rU}***


<br>
<br>

# Penutup
Makasi dah mau baca 
