---
title: FlareOn2 2015 - Challenge 2
author: H0j3n
date: 2023-02-27 00:00:00
categories: [flareon]
tags: [flareon]
render_with_liquid: false
---

This write-up serves as a personal reference and a tool for me to practice FlareOn. It includes information and solutions collected from various sources.

##### Challenge

**Challenge 2.zip** : [Challenge 2.zip](https://github.com/fareedfauzi/Flare-On-Challenges/raw/master/Challenges/2015/Challenge%202.zip)

```bash
# file very_success    
very_success: PE32 executable (console) Intel 80386, for MS Windows
```

Password: `flare`

##### Walkthrough & Solution

When we run the program, it prompts us to enter a password. However, if we enter the incorrect password, the program outputs the message '**You are failure**'. This challenge is almost the same as the first challenge.

```
PS > .\very_success.exe
You crushed that last one! Let's up the game.
Enter the password> password
You are failure
```

Looking at the code for `FUN_00401000`, I noticed that it is similar to the previous challenge, but there is no straightforward **XOR** operation. Instead, our input is used by `FUN_00401084` to perform some operation and return a boolean value.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/7_flareon02_2/flareon02_2_1.png)

```cs
iVar1 = FUN_00401084(unaff_retaddr,&DAT_00402159,local_4)
```

This line will accept 3 arguments:
- First argument = data located at return address
- Second argument = our input
- Third argument = Length of our input

The `jl` instruction at `loc_401096` checks whether the value at `[ebp+arg_8]` (our inputs) is less than `0x25`. If it is, the function jumps to `loc_4010D7`. Otherwise, it continues execution.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/7_flareon02_2/flareon02_2_2.png)

This means our input must be more than `37 (0x25)`

```cs
Input: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA (37)
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/7_flareon02_2/flareon02_2_3.png)

The code above loads the values of `[ebp+arg_4]` and `[ebp+arg_0]` into the `esi` and `edi` registers, respectively. It then calculates the address of the last character of the string and stores it in `edi`. Some similar codes can also be found in **Ghidra**.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/7_flareon02_2/flareon02_2_4.png)

I tried to set breakpoint at the EDI and jump to the hex view. We found these values.

```cs
AF AA AD EB AE AA EC A4 BA AF AE AA 8A C0 A7 B0 BC 9A BA A5  A5 BA AF B8 9D B8 F9 AE 9D AB B4 BC B6 B3 90 9A A8 
```

Some operation involve inside `loc_4010A2` such as `AND`, `XOR` and `ROL`.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/7_flareon02_2/flareon02_2_5.png)

##### A. Solve with Python Script

**Python script (Reverse):**

```python
# Function ROL
def rol(byte, count):
    byte = (byte << count | byte >> (8 - count)) & 0xFF
    # byte = (byte >> count | byte << (8 - count)) & 0xFF (ROR)
    return byte

# Enc Flags
data = "".join(map(chr, bytes.fromhex('AF AA AD EB AE AA EC A4 BA AF AE AA 8A C0 A7 B0 BC 9A BA A5 A5 BA AF B8 9D B8 F9 AE 9D AB B4 BC B6 B3 90 9A A8')))[::-1]

# Initialize Registers
AH = AL = AX = BX = DX = 0
flags = ""

# Loop each Enc Flags
for i in range(0,len(data)):
	AH = rol(1, DX)
	AL = (ord(data[i]) - AH -1) ^ 0xC7
	BX = BX + ord(data[i])
	DX = BX & 3
	flags += chr(AL)
print(flags)
```

**Flag :** `a_Little_b1t_harder_plez@flare-on.com`

##### B. Solve with Angr

##### C. Solve with Pintool

##### D. Solve with IDA Python 

##### References

1. [https://github.com/fareedfauzi/Flare-On-Challenges/blob/master/Write-ups/2015/2015solution2.pdf](https://github.com/fareedfauzi/Flare-On-Challenges/blob/master/Write-ups/2015/2015solution2.pdf)
2. [https://www.tophertimzen.com/blog/flareOn/](https://www.tophertimzen.com/blog/flareOn/)
3. [https://www.ghettoforensics.com/2015/09/solving-2015-flare-on-challenges.html](https://www.ghettoforensics.com/2015/09/solving-2015-flare-on-challenges.html)
4. [https://github.com/angr/angr-doc/blob/master/examples/flareon2015_2/solve.py](https://github.com/angr/angr-doc/blob/master/examples/flareon2015_2/solve.py)
5. [https://secwriteups.blogspot.com/2016/08/flare-on-2015-challenge-2.html](https://secwriteups.blogspot.com/2016/08/flare-on-2015-challenge-2.html)
6. [https://buildfunthings.com/posts/flare-on-2015-challenge-2/](https://buildfunthings.com/posts/flare-on-2015-challenge-2/)
7. [http://0x0atang.github.io/reversing/2015/09/17/flareon2-concolic.html](http://0x0atang.github.io/reversing/2015/09/17/flareon2-concolic.html)
8. [https://www.aldeid.com/wiki/The-FLARE-On-Challenge-2015/Challenge-2](https://www.aldeid.com/wiki/The-FLARE-On-Challenge-2015/Challenge-2)
9. [https://rstforums.com/forum/topic/106101-flare-on-2015-level-2-very_successexe/](https://rstforums.com/forum/topic/106101-flare-on-2015-level-2-very_successexe/)
10. [https://www.redblue.team/2015/11/solving-2015-flare-on-re-contest.html](https://www.redblue.team/2015/11/solving-2015-flare-on-re-contest.html)
11. [https://unhere.com/2015/09/09/flare-on-challenge-2015-2/](https://unhere.com/2015/09/09/flare-on-challenge-2015-2/)