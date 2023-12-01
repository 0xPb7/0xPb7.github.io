---
title: Bugcrowd eLFI Challenge
date: 2023-01-17 11:58:47 +07:00
modified: 2023-01-17 16:49:47 +07:00
tags: [bugcrowd, LFI, php]
---

![image1](https://miro.medium.com/v2/resize:fit:1400/0*OhB8Zp9uhjXWWEaG)

The Bugcrowd has thrown down the gauntlet to all hackers out there.

**HINT-1**: "I am eLFI."

- First, I visited this link: [Bugcrowd Advent Challenge](https://bugcrowd-advent-challenge.herokuapp.com/login.php).
- I attempted to log in with `user1` and `Randompassword123` as credentials, which resulted in an internal server error, as shown below:

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*X7RI3Gd3nMVPhHGeERkWSQ.png)

- I tinkered with the `login.php` page, but no luck.
- I delved into the source code of `login.php` and found a few interesting lines that caught my attention, as highlighted below:

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*VaKBQncUk8AVSV2cPYDwRg.png)

- I remembered the hint, and these lines of code seemed related.
- This led me to discover a vulnerable endpoint: `/style.php?css_file=custom.css.
- It appeared to filter out `/etc/passwd`, so I attempted URL and BASE64 encoding, but no luck.
- After some more exploration, I noticed another file: `index.php.
- When I tried to open it, it redirected me to `login.php. However, in the network tab, I saw `index.php` with a status code of 302.
- I felt suspicious about `index.php` and decided to investigate further:

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*auF6KSYzFoF5xnc5FoBJfw.png)

- I used `php://filter/convert.base64-encode/resource=<filename>` to bypass restrictions and view the source code of `index.php.

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*G3THjATyx7BnY0cNODccTQ.png)

- `index.php` revealed an encoded string, which I decoded using CyberChef:

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*78aQVW839y5s6yl7OscdDA.png)

- After decoding, I found another PHP file: `dashboard.php`. I replaced `index.php` with `dashboard.php:

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*92uErSvzxkxdQzrBB1FaLA.png)

- `dashboard.php` presented another encoded string, which I decoded again.
- After decoding, I found yet another file: `sober.php.

![image](https://miro.medium.com/v2/format:webp/1*Vze78YM-FcbfaSUpNPWq-A.png)

- I replaced `dashboard.php` with `sober.php` and discovered another encoded string in the comments, although smaller than the earlier ones:

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*FtxcGiChOcTDTofb1Dahhg.png)

- After decoding, I obtained the following code: **FLAG{d1g_d33p_and_find_7he_power_within}**:

![image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*kGtqNRgnZS1CgA-iFOp67g.png)

I don't know why, but I was drawn to this challenge and even took some time off work to solve it. Thanks to [Bugcrowd](https://twitter.com/Bugcrowd) for the fun challenge!

The challenge might seem simple after reading this walkthrough, but believe me, it took a lot of brainpower and time to execute.

I'd like to extend my gratitude to my friend, Paweł Wąsik, for his constant support.

**References:**

- [Using PHP Wrappers within LFI to Obtain PHP Script Source Code](https://infinitelogins.com/2020/04/25/lfi-php-wrappers-to-obtain-source-code/)
- [You find a Local File Inclusion (LFI) running PHP, you're able to leverage a PHP wrapper to convert the file to Base64…infinitelogins.com](https://infinitelogins.com/2020/04/25/lfi-php-wrappers-to-obtain-source-code/)

**Connect with me at:** [LinkedIn](https://www.linkedin.com/in/prasanth-bodepu-%E0%B0%AA%E0%B1%8D%E0%B0%B0%E0%B0%B6%E0%B0%BE%E0%B0%82%E0%B0%A4%E0%B1%8D-411ba31a3/), [Twitter](https://twitter.com/_0xPb)