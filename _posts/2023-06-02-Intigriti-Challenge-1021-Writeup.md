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

### Content Security Policy (CSP)

In the response headers, there is no indicator of CSP implemented. But we can see in the page source how the web page implemented the CSP.

```html
<meta
  http-equiv="Content-Security-Policy"
  content="default-src 'none'; 
	  script-src 'unsafe-eval' 'strict-dynamic' 'nonce-a5653f86ab0d00e7ea5297f7264c43c0';
	  style-src 'nonce-51070e634c401773866289cb83ad269e'"
/>
```

While exploring this CSP further, I came across some interesting information about the usage of `strict-dynamic` in **Content Security Policy (CSP)**. You can find this information in [here](https://exploited.cz/xss/csp/strict.php?inserted=parser)

> `strict-dynamic`: Allows script which executes on a page to load more scripts via non-"HTML-parser-inserted" script elements using document.createElement('script'); or similar (CSP3 and above)
{: .prompt-info }

To have a quick check for `strict-dynamic`, we can try compare with and without using `document.createElement` to get an XSS.

```html
<html lang="en">
  <head>
    <meta
      http-equiv="Content-Security-Policy"
      content="default-src 'none'; script-src 'unsafe-eval' 'strict-dynamic' 'nonce-a5653f86ab0d00e7ea5297f7264c43c0'; style-src 'nonce-51070e634c401773866289cb83ad269e'"
    />
  </head>
  <body id="body">
    <!-- POC (XSS) #1 -->
    <script nonce="a5653f86ab0d00e7ea5297f7264c43c0">
        let s = document.createElement("script");
        s.appendChild(document.createTextNode("alert('Using createElement script')"));
        document.body.appendChild(s);
    </script>
    <!-- POC (XSS) #2 -->
    <script nonce-"a5653f86ab0d00e7ea5297f7264c43c0">
        alert('Without createElement script');
    </script>
  </body>
</html>
```

As we can observe, only the XSS in the first Proof of Concept (POC) will be executed successfully, whereas the second one will be prevented from executing due to the Content Security Policy (CSP).. We will come back to this topic in the solution part later.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/9_intigriti/intigriti_1021_6.png)

### Uncaught TypeError

The error occurs because the web is attempting to retrieve the element with the id **"lock,"** but it cannot be found, resulting in the value becoming null.

```html
<script nonce="afedac0a9a5507ad03f76ca4f5d76a81">
document.getElementById('lock').onclick = () =>
	{document.getElementById('lock').classList.toggle('unlocked');
}</script>
```

Despite encountering this error, we can still proceed with solving the challenge. The absence of the **"lock"** element and the resulting null value does not stop us to find a solution.

### Uncaught SyntaxError

This error occurs within the snippet of JavaScript code provided below:

```html
<script nonce="afedac0a9a5507ad03f76ca4f5d76a81">
      window.addEventListener("DOMContentLoaded", function () {
        e = `)]}'` + new URL(location.href).searchParams.get("xss");
        c = document.getElementById("body").lastElementChild;
        if (c.id === "intigriti") {
          l = c.lastElementChild;
          i = l.innerHTML.trim();
          f = i.substr(i.length - 4);
          e = f + e;
        }
        let s = document.createElement("script");
        s.type = "text/javascript";
        s.appendChild(document.createTextNode(e));
        document.body.appendChild(s);
      });
</script>
```

Since the parameter `xss` was not provided, the value of `e` will be `null`. The result variable will hold the value `)]}'`. Consequently, when attempting to find an element with the id **"intigriti"** it won't be found in the page source. Therefore, we can assume that the generated script element will resemble the following:

```html
<script type="text/javascript">
	<script type="text/javascript">)]}'null</script>
</script>
```

So now we know the parameter `xss` will be a useful for us to get an XSS. 

## References

1. [https://blog.isiraadithya.com/intigriti-1021-xss-challenge-solution-writeup/](https://blog.isiraadithya.com/intigriti-1021-xss-challenge-solution-writeup/)