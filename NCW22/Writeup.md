# WRITE UP NATIONAL CYBER WEEK 2022

## Forensic

**1. Downloader**

Pada chall ini kita diberi sebuah file malware yang ternyata sebuah trojan untuk dianalisis untuk menjawab pertanyaan - pertanyaan sebagai berikut untuk mendapatkan flagnya

```
QUESTIONS:
1. What is the name of the Domain that hosted the trojan malware? 
(E.g, http://abc123.com -> "abc123" is the Domain name)

2. What is the file's name of the trojan malware itself?

3. What is the IP Address of the Domain?

4. From what country that the Domain is launched?
```

hal pertama yang kami lakukan adalah menjalankan command `exiftool` pada file trojan tersebut dan didapat informasi berupa full command line yang digunakan untuk mendownload trojan

![commandline](/NCW22/img/commandline.png)

Dari sana kami juga mendapat nama domain yang digunakan untuk menghosting file trojan tersebut : `2filmes.com` dan juga nama file trojan yang didownload : `svchost.exe`. kedua informasi tersebut berhasil menjawabb pertanyaan pertama dan kedua. Setelah mendapat domainnya kami lalu mencari IP Address nya dengan online tools `virustotal.com` 

![ip](/NCW22/img/ip.png)

Dapet lah IP Address nya : `104.37.35.127`. No 3 kelar, lanjut no 4. untuk menjawab soal no 4 kami menggunakan online tool `whois.com` dan didapat klok IP `104.37.35.127` dilaunch dari `Denmark`

![denmark](/NCW22/img/denmark.png)

Setelah dapet semua jawaban. susun jawaban berdasarkan format yang telah diberikan untuk mensubmit flag.

**Flag**

***NCW22{ 2filmes_svchost.exe_104.37.35.127_Denmark}***

<br>

**2. BEC Chitchat**

Pada chall ini kita diberi sebuah file outlook yang harus  dianalisis untuk menjawab pertanyaan - pertanyaan sebagai berikut untuk mendapatkan flagnya

```
1. What's the name of suspected person(attacker) that send the malicious
brochure?
(FULLNAME all lower case + if the name consists of two words like "Ismail
Marzuki" then seperate those with whitespace character)

2. What is the attacker's phone number?
[Example Format = +62..........] -> Country-Code number format

3. What is the Address(FQDN) that is close to the source email(sender)?
(E.g : VG7SCF8EV1D.prod.ncwctf.donat.gula.id)

4. What is the Address(IPv6 Address) that is close to the destination
email(receiver)?
(E.g : a05:a612:2d3:09f1:blah:blah:blah:blah)
```
Butuh waktu lumayan lama untuk tau harus diapain file outlook ini. setelah menyelam di google selama beberapa saat, akhirnya kami mendapat hidayah dan menggunakan command `pffexport` untuk mengeksport file didalamnya supaya bisa dibuka

Setelah dapat file yang dieksport, didalamnya terdapat banyak file, setelah sekian lama  mencari-cari hal yang tidak pasti. akhirnya kami mendapat  beberapa informasi dalam file `Root-mailbox/IPM_SUBTREE/[Gmail]/important/Message00001/InternetHeaders.txt`  yang berisi data penting seperti dibawah

```
Delivered-To: alexsteven2211@gmail.com
Received: by 2002:a05:6a10:8a43:b0:2f4:89f4:8483 with SMTP id dn3csp1147063pxb;
        Sat, 24 Sep 2022 09:01:24 -0700 (PDT)

        From: roger alex <rogergrocery@gmail.com>
To: "alexsteven2211@gmail.com" <alexsteven2211@gmail.com>
Subject:
	=?utf-8?B?QW5ub3VuY2luZyBEaXNjb3VudCAmIE1hcmtldCdzIEJlc3QgU2VjdG9yIA==?=
 =?utf-8?B?8J+ls/CfjJ8=?=
Thread-Topic:
	=?utf-8?B?QW5ub3VuY2luZyBEaXNjb3VudCAmIE1hcmtldCdzIEJlc3QgU2VjdG9yIA==?=
 =?utf-8?B?8J+ls/CfjJ8=?=
Thread-Index: AQHY0C67ak1Mfazcz0mWmNC9kZLTEA==
X-MS-Exchange-MessageSentRepresentingType: 1
Date: Sat, 24 Sep 2022 16:01:18 +0000
Message-ID:
	<HK0PR06MB28674110F1EF273ADE5CDEB8AF509@HK0PR06MB2867.apcprd06.prod.outlook.com>
```
Pada data yang terdapat diatas, kita bisa menjawab 3 pertanyaan: 

1. IP Address receiver : `2002:a05:6a10:8a43:b0:2f4:89f4:8483`.

2. Nama pengirim yang diduga sebagai sang hengker : `roger alex`

3. FQDN dari pengirim : `HK0PR06MB2867.apcprd06.prod.outlook.com`

Informasi diatas akan menjawab pertanyaan No 1,3, dan 4, Untuk menjawab pertanyaan no 2, kami mendapatkannya di dalam file
`Root-mailbox/IPM_SUBTREE/[Gmail]/important/Message00001/Message.html` yang terdapat data sebagai berikut

```
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
</head>
<body>
<div dir="auto"><span dir="auto" style="font-family:-apple-system, HelveticaNeue">We choose the best products and present you with such amazing beveragea and foods just for you!!</span>
<div dir="auto" style="font-family:-apple-system, HelveticaNeue"><br>
</div>
<div dir="auto" style="font-family:-apple-system, HelveticaNeue">We from RogerGrocery invite you to come to our MarketStore to see our products.</div>
<div dir="auto" style="font-family:-apple-system, HelveticaNeue">Why? Because we want the best for you.</div>
<div dir="auto" style="font-family:-apple-system, HelveticaNeue"><br>
</div>
<div dir="auto" style="font-family:-apple-system, HelveticaNeue">Come and have a look on our brochure down in this PDF file.</div>
<div id="ms-outlook-mobile-signature" dir="auto" style="font-family:-apple-system, HelveticaNeue">
<div><br>
</div>
<span dir="auto">---&gt; Contact : Roger (+120932132)</span></div>
<br>
</div>
<div id="ms-outlook-mobile-signature" dir="auto">
<div><br>
</div>
Get <a href="https://aka.ms/AAb9ysg">Outlook for Android</a></div>
</body>
</html>
```
Dalam data diatas kami mendapatkan nomor yang diduga milik sang hengker dan bisa menjawab pertanyaan no 2

4. Nomor sang hengker : `+120932132`

Tinggal menggabungkan semua jawaban tadi untuk mendapatkan flagnya

**Flag**

***NCW22{roger
alex_+120932132_HK0PR06MB2867.apcprd06.prod.outlook.com_200
2:a05:6a10:8a43:b0:2f4:89f4:8483}***

<br>

## Web Exploitation

**1. File & Reading .INC**

Langkah pertama yang  kami lakukan pada challenge ini adalah membuka laman dengan menggunakan IP Address yang telah dilampirkan. Pada laman tersebut,  ketika menggeser ke bawah lagi kita akan menemukan sebuah tombol yang berisi tautan. Kami mencoba menekan tombol `Try it out` dan muncul sebuah laman yang mengharuskan kita untuk meng-input `filepaths`.

![tryitout](/NCW22/img/tryitout.png )

Terdapat beberapa berkas bernama `example` dengan ekstensi yang berbeda-beda. Kami mencoba menginput satu per satu, yang kami dapatkan hanyalah program meng-output `Hello World` dengan tiga ekstensi berkas berbeda. Karena kami mendapatkan pentunjuk bahwa flag terdapat pada berkas `/flag.txt` maka kami mencoba menggali berkas itu lebih dalam dengan cara `../../../flag.txt` tapi yang muncul malah flag palsu

![fakeflag](/NCW22/img/fakeflag.png)

Setelah melakukan eksplorasi di `/robots.txt`, kami menemukan ada file webViewer.js yang ada pada directory web, kammi mencoba mengaksesnya dan mendapatkan sourcode javascript yang memiliki celah. dibawah merupakan potongan kode yang terdapat celah. 

```
for(let x=0; x < 10; x++){
    if(!safePath.includes("../")){
        break;
    };
    if(x==9){
        return "nicetry.txt";
    };
    safePath=safePath.replaceAll("../","");
    hasDotDot = true;
}
```

dimana kita bisa mem-bypass validasi yang pertama dengan mengganti `/flag.txt` untuk mendapatkan flag menjadi `..//flag.txt` karena validasi tersebut akan memfilter `/` dan `../` lalu menghilangkannya.

![FLAG](/NCW22/img/flag.png)

**Flag**

***NCW22{f1L7eR_15_n0T_3n0u9h_1372846}***

<br>

## Misc

**1. Mr. Decryptor**

pada challenge ini kita akan connect melalui netcat ke `103.167.136.75:9944` dan akan dikirimkan data yang berupa biner, hexadecimal, atau base64, kita harus mendecode data tersebut dan menginputnya kembali, hal ini harusnya dilakukan dengan membuat script. namun karena tim kamu belum bisa membuat automasi dan memiliki semangat juang yang tinggi wkwk. jadi ya manualin aja

**Flag**

***NCW22{fuiyoohhh_master_of_crypto_right_here!!!}***

