---
title: FlareOn2 2015 - Challenge 1
author: H0j3n
date: 2023-02-19 00:00:00
categories: [flareon]
tags: [flareon]
render_with_liquid: false
---

This write-up serves as a personal reference and a tool for me to practice FlareOn. It includes information and solutions collected from various sources.

##### Challenge

**Challenge 1.zip** : [Challenge 1.zip](https://github.com/fareedfauzi/Flare-On-Challenges/raw/master/Challenges/2015/Challenge%201.zip)

```bash
# file Flare-On_start_2015.exe 
Flare-On_start_2015.exe: PE32+ executable (GUI) x86-64, for MS Windows
```

Password: `flare`

##### Walkthrough & Solution

When you open the `Flare-On_start_2015.exe `, a license agreement will appear that you will need to accept in order to proceed.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/6_flareon02_1/flareon02_1_1.png)

After accepting the license agreement, the program will prompt you to choose a destination path where you want to extract the contents.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/6_flareon02_1/flareon02_1_2.png)

After extracting the contents, you will find a file named `i_am_happy_you_are_to_playing_the_flareon_challenge.exe`

```bash
# file i_am_happy_you_are_to_playing_the_flareon_challenge.exe 
i_am_happy_you_are_to_playing_the_flareon_challenge.exe: PE32 executable (console) Intel 80386, for MS Windows
```

When we run the program, it prompts us to enter a password. However, if we enter the incorrect password, the program outputs the message '**You are failure**'.

```cs
PS > .\i_am_happy_you_are_to_playing_the_flareon_challenge.exe
Let's start out easy
Enter the password>flareon
You are failure
```

I'm going to open it with Ghidra. If you encounter an issue with a missing OpenJDK 17 installation on your Windows system, you can download it from this website: [https://learn.microsoft.com/en-us/java/openjdk/download](https://learn.microsoft.com/en-us/java/openjdk/download)

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/6_flareon02_1/flareon02_1_3.png)

Based on the decompiled entry function provided, the XOR operation performed in the code snippet will iterate over each byte of the user's input, XOR it with the hexadecimal value `0x7d`, and compare the result with the corresponding byte in another variable.

Let's extract all the information we need and use python to get our flag.

```python
stored = b'\x1f\x08\x13\x13\x04\x22\x0e\x11\x4d\x0d\x18\x3d\x1b\x11\x1c\x0f\x18\x50\x12\x13\x53\x1e\x12\x10'
flag = ""
for i in stored:
	flag+=chr(i^0x7d)
print(flag)
```

**Flag :** `bunny_sl0pe@flare-on.com`

##### References

1. [https://secwriteups.blogspot.com/2016/08/flare-2015-challenge-1.html](https://secwriteups.blogspot.com/2016/08/flare-2015-challenge-1.html)
2. [https://www.ghettoforensics.com/2015/09/solving-2015-flare-on-challenges.html](https://www.ghettoforensics.com/2015/09/solving-2015-flare-on-challenges.html)
3. [https://www.aldeid.com/wiki/The-FLARE-On-Challenge-2015/Challenge-1](https://www.aldeid.com/wiki/The-FLARE-On-Challenge-2015/Challenge-1)
4. [https://github.com/fareedfauzi/Flare-On-Challenges/blob/master/Write-ups/2015/2015solution1.pdf](https://github.com/fareedfauzi/Flare-On-Challenges/blob/master/Write-ups/2015/2015solution1.pdf)
5. [https://m.alvar.es/2015/10/fireeye-flareon-2015-challenges-1-3.html](https://m.alvar.es/2015/10/fireeye-flareon-2015-challenges-1-3.html)
6. [http://nieluj.github.io/flareon2/](http://nieluj.github.io/flareon2/)
7. [http://camelinc.info/blog/2015/08/FLARE-on-Challenge-2015/](http://camelinc.info/blog/2015/08/FLARE-on-Challenge-2015/)
8. [https://www.tophertimzen.com/blog/flareOn/](https://www.tophertimzen.com/blog/flareOn/)
9. [https://blog.wirhabenstil.de/2015/09/09/fireeye-flare-challenge-2015-write-up-level-1/](https://blog.wirhabenstil.de/2015/09/09/fireeye-flare-challenge-2015-write-up-level-1/)
10. [https://blog.csdn.net/weixin_53349587/article/details/122300468](https://blog.csdn.net/weixin_53349587/article/details/122300468)