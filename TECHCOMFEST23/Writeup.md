# WRITE UP TECHCOMFEST 2023

## Web

**1. Note Manager**

Jadi pada chall ini terdapat sebuah web yang memungkinkan kita untuk login dan register, jadi kami mencoba register dan tidak menemukan apa”, lalu kami berniat melakukan `LFI` dengan menambahkan code execution pada username berupa command `<?= system($_GET[“cmd”]); ?>`. Setelah mengotak-atik ternyata terdapat celah `LFI` pada saat kami melihat note yang telah kita buat pada `view_note.php` yang menggunakan query `id=` yang bisa kami inputkan payload, jadi kami menginput payload
`php://filter/convert.base64-encode/resource=index.php`
untuk mengambil kode php dari file `index.php`. dan setelah dianalisa kita bisa melihat file `.user` yang berisi username dan password yang sudah di hash menggunakan md5. Setelah lama berpikir, lalu kami mencoba menggunakan `http://103.49.238.77:57270/view_note.php?id=.user` untuk payload mencoba melihat aktivitas yang masuk ke dalam file `.user` dan didapat banyak username dan password yang berupa md5hash yang sepertinya juga diinput oleh peserta lain. Namun setelah melihat-lihat sampe akhir, saya mendapat keanehan pada tulisan warning system seperti pada gambar dibawah

![WarningSystem](/img/WarnigSystem.png)

Dimana ada tulisan `Warning: system() Cannot execute a blank command`, yang berarti ada command injection yang berhasil namun belum ada command yang dieksekusi. Jadi kami berpikir untuk merubah payload, dan setelah merubah-rubah dan bereksplorasi dengan command apa saja mau dan anehnya ada juga yang nggak mau, mencari cari file flag nya, dan akhirnya didapat payload yang akan menampilkan flagnya pada web. jadi final payload yang kami gunakan adalah seperti dibawah
```
http://103.49.238.77:57270/view_note.php?id=.user&cmd=cat/flag*
```

Flagnya terdapat diantara data dump yang juga ikut keluar pada web

![FinalPayload](/img/finalPayload.png)

**Flag**

***TECHCOMFEST23{PHP_R4c3_m4k3s_m3_f33ls_l1k3_a_r4c3r}***

<br>

## Cryptography

**1. Hashlision**

Untuk menyelesaikan challenge ini, kami membuat solver yang dapat me-generate kata-kata acak yang terdiri dari empat karakter. Fungsi hash_code (terlampir) di-looping dengan argumen berupa kata-kata acak yang di-generate sebelumnya. Dari algoritma tersebut menghasilkan beberapa kata yang memiliki kode hash yang sama dengan `SECRET_WORD` yaitu `nioP`, `njOo`, `njPP`, `oJno`, `oJoP`, `oKOo`, `oKPP`. Berikut merupakan script solver dari challenge ini

```
SECRET_WORD = "nino"
charlist = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"
def hash_code(s):
    h = 0
    for c in s:
        h = (31 * h + ord(c)) & 0xFFFFFFFF
    return h
def main():
    word = ""
    for i in charlist:
        for j in charlist:
            for k in charlist:
                for l in charlist:
                    word = i + j + k + l
                    if hash_code(word) == hash_code(SECRET_WORD) and word != SECRET_WORD:
                        print(word);

if __name__ == "__main__":
main()
```

**Flag**

***TECHCOMFEST23{5uP3r_E4sY_CoLL1s10n}***

<br>

**2. Baby-xor**

Pada chall kali ini kami diberikan source code dan juga flag yang di encrypt sebagai berikut 
```
#!/usr/bin/python
import os

def encrypt(string):
    key = os.urandom(int(len(string) / 5)
    result = ''
    for i in range(len(string)):
        result += chr(ord(string[i]) ^ (key[int(i / 5)] & 0xff))

    return result
if __name__ == '__main__':
    with open('flag.txt', 'r') as f:
         flag = f.read()

    assert len(flag) % 5 == 0

    print(encrypt(flag).encode('latin1').hex())


# ENCRYPTED_FLAG : 14050308032022292a3c472120687147110a2c0bfcbe93bffc4629130c0b
```

Diberikan juga flag yang diencrypt, setelah dianalisa, flagnya harus kelipatan 5, dan kami mengecek pada flag yang dienkripsi ternyata terdapat 60 character, dan itu dalam hex yang berarti flagnya memiliki panjang 30 kata. Setelah itu kami menganalisa key nya, yang ternyata didapat dengan merandom string dengan panjang flag/5. Jadi karena tadiflagnya ada 30 karakter, berarti key nya terdiri dari 6 karakter yang tiap 5 karakter flag pertama di encrypt dengan key index ke i/5 dr index karakter tersebut. Lalu kami menggunakan teknik partial known text,
karena format flagnya `TECKCOMFEST{` lumayan panjang wkwk, kami ambil 10 karakter pertama untuk membrute 2 karakter key pertama. Setelah ada secercah harapan. Kami mencoba brute key sisanya satu persatu lalu mencocokan mana yang kira” bakal jadi flag. Berikut kode programnya mencari flag terakhir setelah didapat 5 key pertama dan mencari bagian flag terakhir.

```
#!/usr/bin/python
import os
from pwn import *


def encrypt(string, guest):
    key = bytes([64, 111, 19, 115, 204, guest])

    result = ''
    for i in range(len(string)):
        result += chr(string[i] ^ (key[int(i / 5)] & 0xff))
    return result


if __name__ == '__main__':
    flag = unhex(
        "14050308032022292a3c472120687147110a2c0bfcbe93bffc4629130c0b")

    assert len(flag) % 5 == 0

    for i in range(255):
        print(i)
        print(encrypt(flag, i).encode('latin1'))
```

Lalu  kami mengecek satu”, hampir putus asa setelah 4x ngescroll ampe habis ga nemu”. Akhirnya setelah pantang menyerah memelototi outputnya. Kami dapat flag nya heheee

**Flag**

***TECHCOMFEST23{5uP3r_E4sY_CoLL1s10n}***

<br>

## Misc

**1. Wordle**

Pada chall ini sebenarnya tidak ada tendensi untuk melakukan exploit segala macem, cuma hv fun dan mengandalkan vocabulary bahasa inggris yang baik.Tapi untuk mempermudah menyelesaikan challenge ini, kami membuat wordlist yang didapat dengan menggunakan script berikut:

```
from pwn import *
import re

for i in range(9999):
    p = remote("103.49.238.77", 34601)

    p.sendline('a')
    p.sendline('a')
    p.sendline('a')

    pattern = re.compile(r"correct word was '(\w+)'")
    matches = pattern.finditer(p.recv().decode())

    correct_answers = []
    for match in matches:
        correct_answers.append(match.group(1))

    with open("wordlist.txt", "a", encoding='utf-8') as file:
        for word in correct_answers:
            file.write(word + "\n")
        file.close()

```

Sebenarnya ada sedikit celah yang bisa kita exploitasi pada chall ini, dimana saat streaknya 10 kita bisa secara terus menerus menambah nyawa sampai dirasa cukup untuk melanjutkan challengenya.

Setelah itu, kami melanjutkan challenge ini dengan menjawab wordle berdasarkan wordlist yang diperoleh. Terdapat beberapa wordle yang memiliki banyak kemungkinan kata, disitu kami menggunakan commands yang tersedia.

![FlagWordle](/img/flagWordle.png)


**Flag**

***TECHCOMFEST23{Fl4G_F0r_Th3_Ch4mPs}***

<br>

**2. Ascii Catch**

Untuk menyelesaikan challenge ini, kami mengambil output dari
challenge dengan cara connect nc lalu menaruh output ke file.txt dengan command berikut :
`nc 103.49.238.77 22103 > file.txt`
Dan menghasilkan file baru yang dapat di-scan serta menghasilkan
flag seperti pada gambar dibawah:

![qrAscii](/img/qrAscii.png)

**Flag**

***TECHCOMFEST23{pLz_d0Nt_t311_m3_th4t_y0u_d3c0de_th1S_m4nu4lLy}***

<br>


## Forensic

**1. Pixel**

Awalnya diberikan sebuah gambar dengan format `PNG`, dan gambar tersebut tidak dapat dibuka. Dikatakan kalau pixel gambar tersebut diurutkan di dalam height yang sama dan gambar tersebut merupakan hasil screenshot. Dari clue tersebut bisa direka-reka kalau sebuah hasil screenshot layer laptop umumnya berada di ukuran `1920 X 1080`. Langkah pertama yang kami lakukan adalah mengubah gambar menjadi bentuk RGBA-nya. Dengan menggunakan kode python.

```
from PIL import Image

im = Image.open("pixel.png","r")
px = list(im.getdata())
with open("flag.txt","w") as file:
    file.write(str(px))
```
Setelah itu dimasukkan ke dalam sebuah file bernama `flag.txt`,
yang isinya berupa `[(255, 255, 255, 255), ….]`, kami membuat
kode python Kembali untuk me-render gambar tersebut.

```
from PIL import Image

data = #flag.txt di sini
image = Image.new('RGBA', (1920, 1080), "white")
image.putdata(data)
image.save("flag.png","PNG")
```
Kode tersebut akan menghasilkan gambar dari pixel yang telah kita pecah dan disusun kembali dengan resolusi `1920x1080` dan ternyata flagnya ada di gambar tersebut. 

![flagPixel](/img/flagPixel.png)


**Flag**

***TECHCOMFEST23{eniwei_lu_ada_rencana_masuk_sunib_ga_ngab_}***

<br>

**2. Mono**

Pada chall ini diberikan sebuah file `.wav`. kami mencoba membuka di `Sonic Visualiser` dan mencoba melihat spectogramnya, awalnya terlihat tulisan flag yang buram, lalu kami mencoba untuk melihat spectogram pada kanal kedua yang terdapat spectogram flag yang terlihat jelas

![flagMono](/img/flagMono.png)

**Flag**

***TECHCOMFEST23{wh0_d03snT_LOV3_F1Ve_N1GhtS_At_fR3DDyS_R1gHt_aNyWay_HeR3_1s_uR_FL4G_a1cd6113}***

<br>

**2. Flag Checker**

pada chall ini kita akan diberikan file `chall.zip` yang jika diunzip berisi dump memory dari android dalam bentuk file `.bin`, setelah mencoba menganalisa log pada file `com.flag.checker-maps.txt` dan meng otak-atik nya dengan `volatility`, kami pun sedikit putus asa dan mencoba keberuntungan dengan men-strings semua dump file yang ada dan mencari flag dengan command seperti pada gambar

![flagChecker](/img/flagChecker.png)

**Flag**

***TECHCOMFEST23{th1S_w4S_m3AnT_T0_b3_r3V3rS1nG_ChAlL_But_0H_w3lL_H3r3_W3_4r3}***

<br>

**3. Qracking**

Oke, jadi chall ini bisa dibilang sangat rumit karena ada banyak
tahap untuk bisa mendapat flagnya. Pertama kita akan diberi
sebuah file dengan ekstensi `.avi` yang berupa video yang berisi
gabungan dari banyak QR code yang muncul secara bergantian.
Setelah melihat metadata pada video dengan `exiftool` didapatkan
data sebagai berikut.

![dataQr](/img/dataQr.png)

Diketahui video tersebut memiliki frame rate 60 fps yang bisa
digunakan untuk mengekstrak semua gambar qrcode yang ada dalam video dengan menggunakan command ffmpeg pada terminal linux.
Command lengkap yang kami gunakan adalah

```
ffmpeg -i chall.avi -r 60 -f -image2 image%3d.jpeg
```

![ffmpeg](/img/ffmpeg.png)

Setelah tu akan didapat 840 file `image.jpeg` yang berisi QR code acak, jadi kami menggunakan python untuk men decodenya 1 persatu karena klo manual capek, lalu hasilnya akan kami input ke `hasil.txt`. Berikut adalah solver yang kami gunakan

```
import cv2
import os

from cv2 import QRCodeDetector
f = open('hasil.txt','a+')
directory = 'dump'

file = os.scandir(directory)
file = list(map(str, file))
file = [x[11:-2] for x in file]
for x in file:
    file = sorted(file)

for filename in file:
    print(str(filename))
    img = cv2.imread('dump/' + filename)
    qr = QRCodeDetector()
    try:
        data, _ , _  = qr.detectAndDecode(img)
    except:
        continue
    print(data)
    if len(data) == 0:
        f.write('\n')
    else:
        f.write(data + '\n') 
```

Dan didapat hasil berupa data dump seperti dibawah yang sangat banyak dan kami pun bingung harus diapain tu data

![dataDump](/img/dataDump.png)

Namun setelah melihat-lihat kembali soal, ada clue yang menyebutkan     `md5`, dan setelah diperhatikan dalam data dump tersebut ternyata terdapat baris-baris yang datanya berupa `hex` dan ketika coba saya decrypt `md5` akan menghasilkan char. Terus mencoba manual dan mulai putus asa karena hasil decryptnya tidak menunjukan indikasi flag, kami iseng untuk mendecrypt `hash` terakhir dan menghasilkan `=`, dan kami pun mendapat ide untuk menggabungkan hasil decrypt `md5 hash` tersebut dan mendecodenya dengan `base64` dan mencoba membuat solver sederhana.
Berikut solver kami

```
import string
import hashlib

wordlist = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz1234567890'

lines = [line.strip('\n') for line in open('hasil.txt', 'r').readlines()]

def is_hex(s):
    hex_digits = set(string.hexdigits)
    # if s is long, then it is faster to check against a set
    return all(c in hex_digits for c in s)

md5decode = ''   
for i in lines:
    for j in wordlist:
        result = hashlib.md5(j.encode())
        if(result.hexdigest() == i):
            md5decode += j
        else:
            continue

is_hex(lines)
print(md5decode)
```

Dan didapatkan output berupa `base64` dan langsung ja di decode tar dpt dah flagnya

![flagQR](/img/flagQR.png)


**Flag**

***TECHCOMFEST23{p4rS1nG_S00_m4nY_QR_c0DeS_1sNt_S0_fUN_4fT3r_4LL}***

<br>

## OSINT

**1. Dewaweb**

Kami menemukan flag tersebut pada kolom komentar di laman Facebook resmi milik Dewaweb.

![flagDewaweb](/img/flagDewaweb.png)

**Flag**

***TECHCOMFEST23{Th4nkS_T0_Dewaweb_F0r_Sp0nS0r1ng_Us}***

<br>

**2. Runaway**

Pada chall kali ini kita disuruh untuk melacak tower dari provider yang dipakai oleh hacker, pada soal kita diberi tahu bahwa providernya itu telkomsel dan `eNB id` nya `248440`, jadi kami langsung mencari di google tower dengan `eNB id` seperti pada soal, dan didapat tower tersebut berada di jalan by pass ngurah rai seperti pada gambar

![enbID](/img/enbID.png)

selanjutnya kita klik open in google maps untuk mencari `longitude` dan `latitudenya`

![longitudeLatitude](/img/longitudeLatitude.png)

didapat `longitude` dan `latitude` nya dan tinggal masukin ke format flagnya


**Flag**

***TECHCOMFEST23{-8.7:115.2}***

<br>

**3. Contact**

Pada challenge ini kita diberikan file `contact.vcfm` dan setelah dilihat isinya seperti ada data dan nomor telepon

![contact](/img/contact.png)

Dari banyak no telepon tersebut kami coba untuk mencari semuanya di `getcontact` dan didapatkan nomor `628988117322` dengan nama `Chariovalda Efstathios` yang ada tag This is the correct answer, jadi kami asumsikan itu flagnya, submit dan benar

![pelaku](/img/pelaku.png)


**Flag**

***TECHCOMFEST{628988117322:Chariovalda Efstathios}***

<br>

<br>


# Penutup

**Makasi dah mau baca**