---
title:  Blink’s Secret
author: Vyshak
date: 2022-03-13
categories: [1337UP]
tags: [osint]
---

Hello Everyone. This is my another blog of solving another challenge of [**1337up CTF**](https://ctf.intigriti.io/) hosted by [**Intigriti**](https://www.intigriti.com/)**.** My team [**TamilCTF**](https://tamilctf.com/) has scored 10th in this CTF.

In this writeup we are gonna see an OSINT challenge **Blink’s Secret.** I am so happy to say that I got first blood for this challenge 😊. Also thanks for [Paul](https://twitter.com/cyberpj1) for collaborating with me in this challenge.

![](https://miro.medium.com/max/1190/1*LmwBpZslP3TtMXGWp3Twwg.jpeg)

Blink’s Secret Challenge

So, In this challenge, they have given a [**note.txt**](https://downloads.ctf.intigriti.io/1337UPLIVECTF2022-894ff411-aff8-453c-87b1-20ea939a7b6c/blinkssecret/7ebf4b6c-e4b3-490d-8a06-5c076906f530/note.txt) file and an [**image**](https://downloads.ctf.intigriti.io/1337UPLIVECTF2022-894ff411-aff8-453c-87b1-20ea939a7b6c/blinkssecret/7ebf4b6c-e4b3-490d-8a06-5c076906f530/meme.jpg). Let’s see what’s in the note.txt file.

![](https://miro.medium.com/max/1268/1*J3u1_3D-_UVoM-HLlYQwjg.jpeg)

note.txt

So the note says we need to find someone’s social media account. They have given certain hints to find the account.

-   _The username comprises of name and the zip-code of current residence_
-   _The username is 15 characters long._
-   _We need to find the name of the blinking man in the image._
-   _The format is_ **_Name_zipcode_**_._
-   _If the name is thomas mueller then write the name as ThomasMueller._

Let’s see what is in that image.

![](https://miro.medium.com/max/1352/1*zAWBiumlqYNtcGa-L37KoQ.jpeg)

image of blinking man

It is the iconic blinking man meme. We need to find his real name.

![](https://miro.medium.com/max/1276/1*btQEz7-DYjisBPioz7uDQg.jpeg)

So his name is Drew Scanlon. So the password will be **DrewScanlon_***.** We need to find his pin-code of current residence. Let’s see his [twitter account](https://twitter.com/drewscanlon).

![](https://miro.medium.com/max/1192/1*dJYpGgbJ4dgncA3cTkg_Rg.jpeg)

While taking a peek at his twitter account, we came to a conclusion that he lives in San Francisco, California. Let’s find the pin-code of [San Francisco](https://www.zip-codes.com/city/ca-san-francisco.asp).

![](https://miro.medium.com/max/1288/1*5bEDL_HIC4W1wk_r8JVgtA.jpeg)

So the pincode is 941***. We need only the first three numbers since the username is 15 characters long. So the username will be **DrewScanlon_941.**

After searching this username in almost all Social Media platforms, I found this account on [twitter](https://twitter.com/DrewScanlon_941).

![](https://miro.medium.com/max/1342/1*_ImRSB2EjQjvAl0RYL4T4g.jpeg)

DrewScanlon_941

Well, the bio says it all. This account has only one tweet. Let’s take a look at that.

![](https://miro.medium.com/max/1324/1*lbT5IVy3qxC1IV78WnI0pg.jpeg)

tweet

That’s a nice hint. In order to see those previous tweets, lets hop into the [Wayback Machine](https://web.archive.org/).

![](https://miro.medium.com/max/1400/1*RH_WSyFnm2Xcadxva1QNNQ.jpeg)

wayback machine

Enter the url of that account and it will show the previous snapshots. We can see the old tweets (actually one tweet in this account 😁).

![](https://miro.medium.com/max/1400/1*oZegZld4Ic529TesvKpPzw.jpeg)

old tweet

That font looks a bit sus isn’t it? I will show you why. Go to [https://holloway.nz/steg/](https://holloway.nz/steg/) . It is a website to hide secret messages in a tweet. Let’s decrypt what’s in it.

![](https://miro.medium.com/max/1400/1*VySpk0hQiThA96w777rkKw.jpeg)

flag

Well we got the flag [🚩](https://emojipedia.org/triangular-flag/).

Well that’s the end of this challenge. Hope you like the blog. If you do, make sure to clap and share to your hacker buddies and tech enthusiasts. Follow me on socials for more awesome content, links given below. Let’s see you in next blog until then **PEACE OUT** ✌️.