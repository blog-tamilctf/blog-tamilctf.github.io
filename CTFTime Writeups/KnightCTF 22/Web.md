---
title: Web
author: godson
date: 2022-01-22
categories: [web]
tags: [web, ctf]
---

# Knight CTF Web Writeups


> CTF Name : KnightCTF 2022

> Category : Web


## Can You Be Admin?


 <img src="https://i.imgur.com/QhlIR3a.png"> <br> 
 
 - Web Page Looks Like this:
    
 
 <img src="https://i.imgur.com/ZRhuVKA.png"> <br>   
 
- Upon changing the header to `KnightSquad`
 
 <img src="https://i.imgur.com/20ve3M6.png"> <br>
 
 Response: `This page refers to knight squad home network. So, Only Knight Squad home network can access this page.`
 
 Now, Adding a Header `Referer: localhost`
 
 <img src="https://i.imgur.com/twBiDJK.png"> <br>
 
 Server Responded with a Login Page:
 
 <img src="https://i.imgur.com/6ytwo4t.png"> <br>
 
 Here, author commented some encoded Stuffs
 
 `[][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]][([][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(!![]+[])[+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])....A Long Encoded String`
 
 By Decoding the with <a href="https://enkhee-osiris.github.io/Decoder-JSFuck/">JSFUCK Decoder</a>


<img src="https://i.imgur.com/pySCUPi.png"> <br>

Again Decode this decoded text with <a href="https://www.dcode.fr/ascii-85-encoding">ascii-85</a>

<img src="https://i.imgur.com/SuvOx86.png"> <br>

Using this creds and Login

<img src="https://i.imgur.com/Q5ohGkn.png"> <br>

There are some Cookie is tracking us

<img src="https://i.imgur.com/N7OfkSu.png"> <br>

`VXNlcl9UeXBl` and `Tm9ybWFsX1VzZXI=` are Base64 Encoded.

`Tm9ybWFsX1VzZXI` -> Base64 Decode -> Normal_User

Now, Encode Admin in Base64 and Replace with Normal_User


> Flag: KCTF{FiN4LlY_y0u_ar3_4dm1N}

---

## My PHP Site

<img src="https://i.imgur.com/cj1EJc7.png"> <br>

Web Page Looks Like :

<img src="https://i.imgur.com/wSHruz6.png"> <br>

In Url, there is a File param, with is vulnerable to to LFI

`file=/etc/passwd` retuns 

<img src="https://i.imgur.com/15svoQG.png"> <br>

As he mentioned, its php site, so, lets View index.php with `php wrappers`

> Payload: php://filter/convert.base64-encode/resource=index.php

<img src="https://i.imgur.com/7t52hPY.png"> <br>

Decode this string with Base64

<img src="https://i.imgur.com/p1pqRRh.png"> <br>

But, the `s3crEt_fl49.txt` in the web root dir.

<img src="https://i.imgur.com/C5Sx3qC.png"> <br>

> Flag: KCTF{L0C4L_F1L3_1ncLu710n} 


## BYPASS! BYPASS! BYPASS!

<img src="https://i.imgur.com/iRIFi9i.png"> <br>

First Look:

<img src="https://i.imgur.com/0cqZu08.png"> <br>


Upon checking the Source Code:

<img src="https://i.imgur.com/52Llqey.png"> <br>

We need to Send a Post Request to `/api/request/auth_token` endpoint

<img src="https://i.imgur.com/3VrjwPO.png"> <br>

It returns a auth_token.

upon adding `X-Authorized-For: <auth_token>` to the request, 

Its, Redirects to `/admin_dashboard`

<img src="https://i.imgur.com/HZl8yf1.png"> <br>


> Flag: KCTF{cOngRatUlaT10Ns_wElCoMe_t0_y0ur_daShBoaRd}


## Find the Pass Code 2

<img src="https://i.imgur.com/xK4aom9.png"> <br>


This is the 2nd Version of a another chal.

First Look:

<img src="https://i.imgur.com/oOKOhNY.jpg"> <br>

He had Mentioned already, that  `?source` will give the Source 

Source:

<img src="https://i.imgur.com/pYAZZTm.png"> <br>

There is something called PHP type juggling.

`Loose Comparison` and `Strict Comparison` 

<a href="https://www.youtube.com/watch?v=-1kftH6t5VA">Learn More</a>

Magic String Used : `0e1137126905`

<img src="https://i.imgur.com/9m4qZKY.png"> <br>



> Flag: KCTF{ShOuD_wE_cOmPaRe_MD5_LiKe_ThAt__Be_SmArT}


## Most Secure Calculator - 2 

<img src="https://i.imgur.com/omXbFS7.png"> <br>

First Look:

<img src="https://i.imgur.com/SKjVdrN.png"> <br>

Upon Viewing the Source:

<img src="https://i.imgur.com/7hT2Tfz.png"> <br>

Looks Like, Some Regex is filtering the Inputs

Note:  `Only Numbers and Symbols are Accepted`, `Our Input is Passed to eval()`

So, We are Going to Encode Our payload into Octal

<img src="https://i.imgur.com/yLEpoS5.png"> <br>

Here, `( ) '` are allowed, So, we not need to encode these characters

Then, Remove White Spaces and add `\` and add `"` before a `()`


So, Our Payload: `"\163\171\163\164\145\155"("\143\141\164\40\146\154\141\147\56\164\170\164")`

<img src="https://i.imgur.com/H3e0w2S.png"> <br>

> Flag: KCTF{sHoUlD_I_uSe_eVaL_lIkE_tHaT}

    