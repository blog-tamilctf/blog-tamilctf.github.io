---
title: KnightCTF Digital-Forensics Writeup
description: Cool Beginner Friendly Challenges from KnightCTF{2022}
author: cyberpj
date: 2022-01-22
categories: [ctftime]
tags: [png,chuck,ftp,log,forensics,binwalk]
---

### Hello  Amazing CTF Players

**Lets have a look at some Begginer Friendly forensic challenges**
[![https://imgur.com/qgyJgLJ.png](https://imgur.com/qgyJgLJl.png)](https://imgur.com/qgyJgLJl.png)
### CTF Name : KnightCTF 2022
### Category : Digital-Forensics
---

## 1.Compromised FTP 
![image](https://user-images.githubusercontent.com/72292872/150627891-f338a76a-5c43-4523-b230-e538a6bda6b5.png)


**Given:**

```bash
file ftp.log 
ftp.log: ASCII text
```
> They asked for the correct login username and ip address

```
strings ftp.log|grep OK
Mon Jan  3 15:24:13 2022 [pid 5399] [ftpuser] OK LOGIN: Client "::ffff:192.168.1.7"
```

<details><summary>Flag: </summary>
<pre><code class="language-bash">KCTF{ftpuser_192.168.1.7}</code></pre>

</details>

---


## 2. Lost Flag

[![https://imgur.com/clXrXPC.png](https://imgur.com/clXrXPC.png)](https://imgur.com/clXrXPC.png)

**Given:**

```
file Lost\ Flag.png 
Lost Flag .png: PNG image data, 1200 x 600, 8-bit/color RGBA, non-interlaced
```
>You can use stegoveritas , aperisolve.fr or stegsolve

**Here im using stegsolve**

**File ->  Open -> image.png**

[![https://imgur.com/hwUqdlp.png](https://imgur.com/hwUqdlpl.png)](https://imgur.com/hwUqdlpl.png)
<details><summary>Flag: </summary>
<pre><code class="language-bash">KCTF{Y0U_F0uNd_M3}</code></pre>
</details>


----

## 3. Lets Walk Together
![image](https://user-images.githubusercontent.com/72292872/150627915-5a9ad620-d149-4b84-88b6-9493d88a8f14.png)

> From the challenge name you can conclude that its exactly about binwalk !

```
└─$ binwalk -e interesting_waves.png                                                                   

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 1024 x 768, 8-bit/color RGBA, non-interlaced
69968         0x11150         Zip archive data, at least v1.0 to extract, name: Flag/
70031         0x1118F         Zip archive data, encrypted at least v1.0 to extract, compressed size: 37, uncompressed size: 25, name: Flag/flag.txt
70313         0x112A9         End of Zip archive, footer length: 22
```

**get into the extracted file directory and do fcrackzip**
                                                                                                                
> cd _interesting_waves.png.extracted                                                                   
  
```bash
┌──(kali㉿kali)
└─$ ls                                                                                                      
11150.zip  63  63.zlib  
```  

**Zip file is passwod protected**

```
┌──(kali㉿kali)
└─$ fcrackzip  -D -p  /usr/share/wordlists/rockyou.txt -u 11150.zip                                    

PASSWORD FOUND!!!!: pw == letmein!
                                                                                                                
┌──(kali㉿kali)
└─$ unzip 11150.zip                                                                                         1 ⚙
Archive:  11150.zip
[11150.zip] Flag/flag.txt password:  [letmein!]
 extracting: Flag/flag.txt                                                                                                                       
```

**Thats all**

<details>
<summary>Flag: </summary>
 <pre><code class="language-bash">KCTF{BiNw4lk_is_h3lpfUl}</code></pre>

</details>


## 4.Unknow File
![image](https://user-images.githubusercontent.com/72292872/150627938-8280c1ba-e8fd-4e16-8641-abc6ab6fc36f.png)


**Just Correct the first 4 magic bytes of the given file**

`89 50 4E 47`

```
┌──(kali㉿kali)
└─$ xxd unknown\ file|head                                                                             
00000000: 0010 5665 0d0a 1a0a 0000 000d 4948 4452  ..Ve........IHDR
00000010: 0000 04b0 0000 0258 0806 0000 0072 e61f  .......X.....r..
00000020: 1a00 0000 0173 5247 4200 aece 1ce9 0000  .....sRGB.......
00000030: 0009 7048 5973 0000 0ec4 0000 0ec4 0195  ..pHYs..........

          (after)
                                                                                                                
┌──(kali㉿kali)
└─$ xxd unknown\ file|head
00000000: 8950 4e47 0d0a 1a0a 0000 000d 4948 4452  .PNG........IHDR
00000010: 0000 04b0 0000 0258 0806 0000 0072 e61f  .......X.....r..
00000020: 1a00 0000 0173 5247 4200 aece 1ce9 0000  .....sRGB.......
00000030: 0009 7048 5973 0000 0ec4 0000 0ec4 0195  ..pHYs..........
```
![image](https://user-images.githubusercontent.com/72292872/150628131-3e1a9906-cbc1-4ae9-99bf-c346e87918d1.png)

<details><summary>Flag: </summary>
<pre><code class="language-bash">KCTF{Imag3_H3ad3r_M4nipul4t10n}</code></pre>

</details>


 Reference about magic bytes manipulation :
 
 [INCTF-Junior-Finals](https://0xcyberpj.me/my-blog/ctf/INCTF_Junior-Finals/#10-chunking-up)
 
 [INCTF_Nationals-LR](https://0xcyberpj.me/my-blog/ctf/INCTF-21-Forensics/#9chunkies)
 
 [INCTF-Pro-Finals](https://0xcyberpj.me/my-blog/ctf/INCTF_Pro-Finals/#3-chunklet-100pts)
 
 
 ---





