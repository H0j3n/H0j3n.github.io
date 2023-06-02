---
title: Intigriti - Challenge 1021 Writeup
author: H0j3n
date: 2023-06-02 00:00:00
categories: [ctf]
tags: [ctf,writeup,intigriti]
render_with_liquid: false
---

This is my first attempt at the intigriti challenge. I'm writing this to learn and share my experience with others. I've included information from various sources to provide a comprehensive overview.

## Intigriti - Challenge 1021

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/9_intigriti/intigriti_1021_1.png)

Link to challenge: [https://challenge-1021.intigriti.io/](https://challenge-1021.intigriti.io/)

By viewing the page source, we can observe that the **iframe** leads to `challenge.php`.

```html
<div class="card-container">
      <iframe src="challenge/challenge.php" width="100%" height="700px"></iframe>
</div>
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/9_intigriti/intigriti_1021_2.png)

Link to **challenge.php**: [https://challenge-1021.intigriti.io/challenge/challenge.php](https://challenge-1021.intigriti.io/challenge/challenge.php)

The challenge page states that the `html` parameter can be utilized to convey messages.

```html
ARE YOU SCARED?
ARE YOU STILL SANE?
NOBODY CAN BREAK THIS!
NOBODY CAN SAVE INTIGRITI
I USE ?html= TO CONVEY THESE MESSAGES
I'LL RELEASE INTIGRITI FROM MY WRATH...
... AFTER YOU POP AN XSS
ELSE, INTIGRITI IS MINE
```

To verify this, we can send a simple value such as **"POC"** in the `html` parameter. Upon inspection, we can observe that the string **"POC"** is indeed reflected on the web page.

- [https://challenge-1021.intigriti.io/challenge/challenge.php?html=POC](https://challenge-1021.intigriti.io/challenge/challenge.php?html=POC)

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/9_intigriti/intigriti_1021_3.png)

```html
<div id="html" class="text"><h1 class="light">POC</div>
```

Let's attempt **HTML injection** by testing with a basic HTML snippet to see if we can successfully inject HTML code into the page. Here's an example of a test code:

- [https://challenge-1021.intigriti.io/challenge/challenge.php?html=%3Ch1%3E%3Cs%3EPOC%3C/s%3E%3C/h1%3E](https://challenge-1021.intigriti.io/challenge/challenge.php?html=%3Ch1%3E%3Cs%3EPOC%3C/s%3E%3C/h1%3E)

```html
<h1><s>POC</s></h1>
```

By sending this code as the value for the **html** parameter, we can verify the HTML injection occurs.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/9_intigriti/intigriti_1021_4.png)

```html
<div id="html" class="text"><h1 class="light"><h1><s>POC</s></h1></div>
```

However, it seems that even though the `<script>` tag is not filtered, a simple XSS code like below does not execute as expected.

```html
<div id="html" class="text"><h1 class="light"><script>alert(1)</script></div>
```

Upon inspecting the element, we can observe that there are a few errors present.

- `Content Security Policy`
- `Uncaught TypeError: document.getElementById(...) is null`
- `Uncaught SyntaxError: expected expression, got ')'`

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/9_intigriti/intigriti_1021_5.png)

Let's take a look at each of these errors and identify why it give us an error.

##### Content Security Policy (CSP)

##### Uncaught TypeError

##### Uncaught SyntaxError

### References

1. [https://blog.isiraadithya.com/intigriti-1021-xss-challenge-solution-writeup/](https://blog.isiraadithya.com/intigriti-1021-xss-challenge-solution-writeup/)