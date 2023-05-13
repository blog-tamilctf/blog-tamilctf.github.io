---
title: Quiz 
author: 0xRaminf0sec
date: 2022-03-13
categories: [1337UP Live]
tags: [Race condition]
---


> Chall Name : Quiz

> Description : Ready for a little quiz?

On visiting the challenge link,it shows like a normal quiz page which contains 3 questions each questions have 10 marks .Even if we answer correctly for all the questions ,we have only 30 points.But we need 100 points to buy the flag!!.

![Screenshot](https://raw.githubusercontent.com/Ramalingasamy012/WebCTF-writeups/main/screenshots/quiz1.png "Screenshot")

### Race Condition 
>A race condition attack happens when a computing system that's designed to handle tasks in a specific sequence is forced to perform two or more operations simultaneously. Eventually, the application is forced to perform unintended actions. This leads the application to security exploitation.

So,We have to perform race condition here to obtain extra points for each question's.Choose the answer correctly and 
intercept the request with burp and send that request to intruder and add the NULL payloads in Referrer header and start the attack,You can see the extra points added  !!!.

![Screenshot](https://raw.githubusercontent.com/Ramalingasamy012/WebCTF-writeups/main/quiz2.png "Screenshot")

![Screenshot](https://raw.githubusercontent.com/Ramalingasamy012/WebCTF-writeups/main/quiz3.png "Screenshot")

Repeat this steps for remaining two questions ,We obtain 100 points !!.

```
Flag : 1337UP{this_is_a_secret_flag}
```
    
    