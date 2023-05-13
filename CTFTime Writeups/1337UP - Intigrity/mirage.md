---
title: Mirage
author: Vyshak
date: 2022-03-13
categories: [1337UP]
tags: [misc]
---

Hi Everyone. This time I played [**1337up CTF**](https://ctf.intigriti.io/) by [**Intigriti**](https://www.intigriti.com/) and my team [**TamilCTF**](https://tamilctf.com/) placed 10th in it 😊. Let me show how I solved one of the challenges in it, _Mirage - Misc._

![](https://miro.medium.com/max/1180/1*57AAjNDp--yGpMJnyAQ_ig.jpeg)

Mirage - Miscellaneous

Let’s visit the link and see what they have for us.

![](https://miro.medium.com/max/1400/1*TbXaMB2mNeqp_BwgCf2gsA.jpeg)

While looking it plainly, we can see it’s just a website. let’s look at the page source.

![](https://miro.medium.com/max/1400/1*yy8zipOkDXvmq9q_CrPSIQ.jpeg)

A small part of the page source

While casually reading the page source, I have seen the word **ROBOTS** hidden in the website, so I thought of checking whether there is a robots.txt file in it and it is there in the website.

![](https://miro.medium.com/max/1330/1*Sk4kLqZBjQmN_-TuhIHDwg.jpeg)

robots.txt

So, there are a few directories listed in it. I saved those all in a separate text file.

![](https://miro.medium.com/max/574/1*XLMLY-SJFxwa6GnBp0D73w.jpeg)

saved text file from robots.txt

I had a feeling that most of them would be 404’s and rabbit holes so I fired up Burpsuite to use Intruder and brute-force those directories.

![](https://miro.medium.com/max/1400/1*PJB9r_9lbDzkbY0b_7E3Sw.jpeg)

the captured request which is send to Intruder

![](https://miro.medium.com/max/1400/1*r13RhAxOcZzFFdENdQi9zA.jpeg)

adding the payload position in Intruder

![](https://miro.medium.com/max/1400/1*rsq-7fYOVAwmGYSh4-5x0Q.jpeg)

The saved directories are used as payloads to brute-force

![](https://miro.medium.com/max/1400/1*fDGVrCuylkqq3l4XlpDRZw.jpeg)

Intruder’s result

As we can see, there are certain directories which only gave 200 status codes. Let’s look them one by one.

At first, we are gonna see **/flag.txt**.

![](https://miro.medium.com/max/1400/1*bp8VaX0OVERtXew_JEqd0g.jpeg)

flag.txt

So there are certain characters given as flag. lets use cyberchef too find what it is.

![](https://miro.medium.com/max/1400/1*Rr9GZlbu5-oPtoc4ljiPiw.jpeg)

cyberchef result

Of course it is a rabbit hole to troll us 😐. Well, it’s not going to be that easy. Let’s look at the second one **/flag1.txt**.

![](https://miro.medium.com/max/1108/1*Jxx_yDNeOGQI8rYWVTyuhQ.jpeg)

flag1.txt

So, they have given some gibberish. If you read the hint, _“Rot Rot Everwhere but not a single Rot to see”_ we know that we should use Caesar Cipher. I use [cryptii.com](https://cryptii.com/), but use can use any website you prefer.

![](https://miro.medium.com/max/1400/1*qM-0nBD2b98EJ4t6q6Vipw.jpeg)

Caesar cipher result

Another rabbit hole 😑. Let’s see the next one **/wordlists.txt**.

![](https://miro.medium.com/max/1022/1*ma5NkzEEuRIb7WANddMseg.jpeg)

wordlists.txt

They have given a wordlist saying this will help us later. So, I saved it in a separate file. Now to the last one **/ok.txt**.

![](https://miro.medium.com/max/918/1*E1g-cfvguECGLJDGWHalzw.jpeg)

ok.txt

Another hint. In this they have given a directory which is encrypted using Caesar Cipher (_/uncclzrny.wct_). Let’s use cryptii again.

![](https://miro.medium.com/max/1400/1*XVN1FcIT62r192rZbTfy0w.jpeg)

decoding using cryptii

We have found the plaintext which is happymeal.jpg. Let’s see what is in it.

![](https://miro.medium.com/max/1238/1*wyp_M_iLbJqdsCYSwkD4ww.jpeg)

happymeal.jpg

If you visit the link [https://mirage.ctf.intigriti.io/happymeal.jpg](https://mirage.ctf.intigriti.io/happymeal.jpg) this image is shown. Let’s visit **/HelpMeOut.txt**.

![](https://miro.medium.com/max/1400/1*wagrsTddvn0sm5ga3FB1Ew.jpeg)

**HelpMeOut.txt**

Finally 😀. they have given a link to download the flag.zip file. let’s download and extract it.

![](https://miro.medium.com/max/1196/1*tviZaCMedCMieCA1PLBQRg.jpeg)

> flag.zip

The zip file is password protected. We need to use the wordlist **wordlists.txt** to find the password. We are gonna use john to crack the zip file. First let’s make a file which is compatible for john to crack using zip2john and crack the file using wordlists.txt.

![](https://miro.medium.com/max/1400/1*r6q5v9uHkzhktY6-iP8CTw.jpeg)

cracked password using john

So we have found the password which is **_Soeasypeasy214._** Now use the password to open the flag.txt and **BOOM!** , we got the flag.

![](https://miro.medium.com/max/1044/1*aeBYIylH8KTx-S45Se9FZA.jpeg)

>  flag.txt 😄

So this is how I solved Mirage challenge. Hope you guys like this blog. Make sure to show your support by applauding and sharing the blog with your fellow hackers and tech geeks. Let’s see you in another blog, until then **PEACE OUT** ✌️