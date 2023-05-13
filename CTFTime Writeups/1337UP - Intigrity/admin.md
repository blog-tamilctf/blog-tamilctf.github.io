---
title: Getting admin access 
author: 0xRaminf0sec
date: 2022-03-13
categories: [1337 Shop]
tags: [Nosqli,authentication bypass]
---


> Chall Name : Getting admin access 

> Description :  Bling bling bling, I bet you're not 1337 enough to shop here! Wait, you need to change /etc/hosts to get this one working??? Yes! To become an admin, you should use /admin

As per description,we have to add the host in /etc/hosts file.First ping the 1337shop.intigriti.io ,we got the IP of that domain ,add that to /etc/hosts file.

![Screenshot](https://raw.githubusercontent.com/Ramalingasamy012/WebCTF-writeups/main/screenshots/1337shop1.png "Screenshot")

And we have the endpoint /admin. On visiting this endpoint it redirects to the login page and i tried lots of ways to bypass this login page but i can't !!.My teammate 0xGodson spoke about Nosqli for some other challs.

I searched for Nosqli payloads and i ends up with this payload
```
{"username": {"$gt":""}, "password": {"$gt":""}}
```

I got logged in !!.

![Screenshot](https://raw.githubusercontent.com/Ramalingasamy012/WebCTF-writeups/main/screenshots/1337shop2.png "Screenshot")

View the PageSource and there's  a flag.

```
FLAG{NOSQL_INJECTION_IS_AWESOME_IKODSD}
```
    