# crypto/cupcakes

## Problem: 

You have to make 100 cupcakes for your upcoming end of year party and you only have 3 hours to do so after oversleeping the night before. However you are in luck because ShopRite just released their Magic Flour that makes any sort of batter immediately. In order to get the Magic Flour you have to solve a puzzle where you decode the message "avxmsvyusbyxj" using the key word SIFT. Time is ticking!

*Note: wrap the result you get in the flag format in all lowercase, eg. flag{example}*

## Solution: 

Since the message is a bunch of random characters, we hope this is just a simple cipher. Let's use [Boxentriq's Cipher Identifier](https://www.boxentriq.com/code-breaking/cipher-identifier) to get some insight.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/6e14a965-7329-44b4-baa1-8298646a78e0)

Let's see if the Bifid Cipher is any help to us.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/cafd9738-ce97-4c25-8bdc-b28d5071da15)

Darn, still a bunch of random letters. Next, the Beaufort Cipher.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/2ec2c0d0-3836-4908-a0b7-46e744c2a920)

At this point I'm just thinking this is probably not the right approach. Let's see if we can get anything out of the Vigenere Cipher, else we might be out of luck.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/9758f3d5-1a14-4297-b615-0bfa07d606eb)

Huh, those actually look like real words, and they relate to cupcakes. Testing this, we find this is our flag!

## Flag:

`flag{instantbatter}`