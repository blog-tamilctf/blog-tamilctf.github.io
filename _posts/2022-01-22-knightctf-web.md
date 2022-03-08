---
title: KnightCTF Web Writeups - Part 1
author: gokulap
date: 2022-01-22
categories: [web]
tags: [web, ctf]
---

# Knight CTF Web Writeups


> CTF Name : KnightCTF 2022

>  Category : Web


### Hello CTF Players ! Lets See the Web Writeups of KnightCTF 2022
 
 <img src="https://oshi.at/xZhV/all.jpeg">
    
 ## 1. Something you need to look wayback 
<img src="https://oshi.at/RYRh/1.1.png">
 
 Given The Link of the site Let's open it 
 

<img src="https://oshi.at/htLJ/1.2.png">
 
 This Site Looks like a static site and no links are working in it, Its just a Fake static HTML Page
 , Lets look at that source code of the page
 

<img src="https://oshi.at/dSAf/1.3.png">

In the source code we can see the github repo link, Lets open it in new tab, But as usual there is no info here and looked at all the source files in that repo but no Flag here !!

<img src="https://oshi.at/Uhft/1.4.png">

Then Remembered the Title of this challenge `Need to look wayback` Suddenly checked the Commit history of that Repo, Yay Flag is in the commit history !


<img src="https://oshi.at/iGjc/1.5.png">
 
 > Flag : KCTF{version_control_is_awesome}

<hr>

## 2. Do Something Special 

<img src="https://oshi.at/cVQZ/2.1.png">

Given the Website link Lets open it 

<img src="http://oshi.at/Sffw/2.2.png">

There was a button named `Get the Flag` Clicked the button which redirected me to /gr@b_y#ur_fl@g_h3r3! which gave 404 Not found !!

<img src="http://oshi.at/YZAv/2.3.png">

Then I tried to see what Request is Going on So Proxied with Burp and intercepted the Request, The Get Request was like this

<img src="http://oshi.at/cjzj/2.4.png">

Then I Noticed that the Path which it requested was `gr@b_y` But Actual path is `gr@b_y#ur_fl@g_h3r3!` Then I came to know that it was because of Special characters, So i tried to Encode the Path with URL Encoding

<img src="http://oshi.at/Xiia/2.5.png">

Now opened the URL with /{URL ENCODED VALUE}, And Yay We have got the Flag 

<img src="http://oshi.at/teKE/2.6.png">


> Flag : KCTF{Sp3cial_characters_need_t0_get_Url_enc0ded}

<hr> 

## 3. Obfuscation isn't Enough

 
    
    
    