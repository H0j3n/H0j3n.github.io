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

![[intigriti_1021_1.png]]

Link to challenge: [https://challenge-1021.intigriti.io/](https://challenge-1021.intigriti.io/)

By viewing the page source, we can observe that the **iframe** leads to `challenge.php`.

```html
<div class="card-container">
      <iframe src="challenge/challenge.php" width="100%" height="700px"></iframe>
</div>
```

![[intigriti_1021_2.png]]

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

![[intigriti_1021_3.png]]

```html
<div id="html" class="text"><h1 class="light">POC</div>
```


### References

1. [https://blog.isiraadithya.com/intigriti-1021-xss-challenge-solution-writeup/](https://blog.isiraadithya.com/intigriti-1021-xss-challenge-solution-writeup/)