# WRITE UP CYBER JAWARA 2022

## Misc

**1. Your ImageNation**

Untuk mendapatkan flag dari challenge ini kami melakukan analisa gambar ImageNation.png.Sesuai dengan clue pada soal yang memberitahu kemungkinan ada pesan tersembunyi pada soal, kami mencoba untuk menggunakan exiftool untuk melihat jika pesan tersembunyi terdapat pada metadata dari gambar, namun tidak membuahkan hasil. Kami pun mengira bahwa pesan yang dimaksud disembunyikan dengan teknik `steganography`. Kami akhirnya menggunakan command `zsteg` pada gambar dan didapat data dump dalam jumlah yang lumayan banyak namun menyerupai format dari flag

![Dump](/CJ2022/img/dump.png)

APAPUN MASALAHNYA, `GREP` SOLUSINYA


Setelah menemukan secercah harapan, kami langsung menggunakan `grep` untuk mengambil `string` yang merupakan flag nya dengan command `zsteg –-all ImageNation.png | grep CJ2022{` dan didapat flagnya

![Flag](/CJ2022/img/flag.png)

**Flag**

***CJ2022{W35H!_y0000_WeSHHHi!}***
<br>

# Penutup
Makasi dah mau baca
