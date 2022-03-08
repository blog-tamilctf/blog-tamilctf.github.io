---
title: Inctf21 Pro Finals Forensics Writeup
description: Beginner Level Interesting Challenges From INCTF21 PRO Finals
author: cyberpj
date: 2021-12-18
categories: CTF Events
tags: [forensics,peepdf,wireshark,steganography,deepsound,PNG,MagicBytes,cipher]
---


## Hello Amazing Hackers!
> In this Blog We are going to have  a look at  some cool forensic challenges from inctf21 professional by bi0s
>  Beginner Level Interesting Challenges From INCTF21 PRO Finals

------

## 1. XOXO (100pts)

![image](https://user-images.githubusercontent.com/72292872/148693842-704f849b-e24b-48be-ac03-42b92a0bb227.png)

Description: 
```Did you know: if a^b=c then a^c=b```

 **Given : xorred png file**
 
 ``` 
 pj@kali~ file encrypted.png

encrypted.png: data 
```
1. **We actually Dont know the key , but we know the actual extension(png) which is come after the xor process.
then xor the file with magic bytes of png , it will gives the Key to retrive the PNG**
**Magic Bytes of PNG**
```
xxd -l 10 chall.png                                                                  
00000000: 8950 4e47 0d0a 1a0a 0000                 .PNG......
```
![image](https://user-images.githubusercontent.com/72292872/148690219-508bbf5a-229b-4e6f-a013-983876ff7667.png)

2.**Xor the encrypted.png with the key** `eAsy-x0r`
```
>>> from pwn import  *
>>> encrypted=open("encrypted.png","rb").read()
>>> answer=open("flag.png","wb")
>>> answer.write(xor("eAsy-x0r",encrypted))
396068
>>> answer.close()
```
![image](https://user-images.githubusercontent.com/72292872/148690415-6962076d-53f9-47cf-b318-dc71dccf3da2.png)

**Here We go ,FLAG :**`inctf{x0riNg_iS_fUn!!}` 

----

## 2. Look Deeper (200pts)

![image](https://user-images.githubusercontent.com/72292872/148693814-889495f2-0716-4764-b182-2bbe68afd6c1.png)


**Description** : 
``` Ramesh sent me this PDF and told me that there is a weird sound deep in it, and asked me to find it. Can you help him find the what is hidden deep?```

*Given* : 
```
chall.pdf: PDF document, version 1.1
```
1. Evince the pdf
![image](https://user-images.githubusercontent.com/72292872/148691104-aea24cec-cfee-4dfe-bc6b-c4ce0c10bc5b.png)
```
echo "ZTN5bl83a2RfdjBfc240el9nYzdfaHUzeXp0=="|base64 -d                                       
e3yn_7kd_v0_sn4z_gc7_hu3yz
```
>I thought vigenere,
> yes `vigenere cipher` 

![image](https://user-images.githubusercontent.com/72292872/148691273-e2708241-4a4d-469c-8341-7e1b473b9828.png)

well, it's not a flag but let's take it `w3ll_7ry_n0_fl4g_bu7_us3ful`

2. Lets enum the pdf and extract any file

``` 
python2 /opt/git.peepdf/peepdf.py -i chall.pdf

PPDF> info

Version 0:
        Catalog: 1
        Info: No
        Objects (8): [1, 2, 3, 4, 5, 6, 7, 8]
        Streams (2): [5, 8]
                Encoded (1): [8]
        Suspicious elements:
                /Names (1): [1]
PPDF> stream 8 > output
```
**wave time !**
```
file output 
output: RIFF (little-endian) data, WAVE audio, Microsoft PCM, 16 bit, stereo 44100 Hz
```
**From Description `deep?` exactly deepsound!**

3. open the wave in deepsound and input the decrypted cipher as a password

>- open carrier file
>- input password
>- save the flag.zip
![image](https://i.imgur.com/bvm9tNl.png)
- unzip flag.zip
**correct the header of flag.png**
**remove the line `......JFIF......` from flag.png using ghex**

```
>>pj@kali~: xxd flag.png|head                                                                              1 ⨯
00000000: ffd8 ffe0 0010 4a46 4946 0001 0100 0001  ......JFIF......
00000010: 8950 4e47 0d0a 1a0a 0000 000d 4948 4452  .PNG........IHDR
        (after)

 >>pj@kali~: xxd flag.png|head
00000000: 8950 4e47 0d0a 1a0a 0000 000d 4948 4452  .PNG........IHDR
00000010: 0000 03c0 0000 02bd 0806 0000 00eb 1d05  ................
00000020: 3000 0036 837a 5458 7452 6177 2070 726f  0..6.zTXtRaw pro
```

![image](https://user-images.githubusercontent.com/72292872/148692558-235ce7a2-86db-439d-8d9f-2f452d2ffdda.png)

### Flag : inctf{Kn0w1ng_4b0ut_PDF's_15_u53fuL}

-----

## 3. Chunklet (100pts)



[reference-inctf-nationals Learning Round](https://0xcyberpj.me/my-blog/ctf/INCTF-21-Forensics/#9chunkies)

**given:**
```
 >>pj@kali~: file chall.png                                                                               130 ⨯
chall.png: data
```
1. Correct the png header 

```
>>pj@kali~: xxd -l 10 chall.png 
00000000: 8950 6e47 0d0a 1a0a 0000                 .PnG......
                  (after)                                                                                              
 >>pj@kali~: xxd -l 10 chall.png
00000000: 8950 4e47 0d0a 1a0a 0000                 .PNG......
```
2. Replace `idhr` to `IHDR`

3. Replace `IADT` to `IDAT`

![image](https://user-images.githubusercontent.com/72292872/148693353-0c71e455-ec30-4e98-bc1f-9fd45d2dddfb.png)

4. Replace The trailer from  `INED` to `IEND`

```
>>pj@kali~: xxd chall.png |tail                
00006de0: 0000 494e 4544 ae42 6082                 ..INED.B`.
                             
                             (AFTER)                           
 >>pj@kali~: xxd chall.png |tail
00006de0: 0000 4945 4e44 ae42 6082                 ..IEND.B`.
```
5. `feh chall.png`

![image](https://user-images.githubusercontent.com/72292872/148693591-7a6e6500-94a9-454b-adbb-aa41a12cbfe5.png)

**Here we Go**
### Flag : inctf{tH15_w4S_Pr3Ty_EA5y}

-----
