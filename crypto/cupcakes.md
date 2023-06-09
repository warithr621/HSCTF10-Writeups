# crypto/cupcakes

## Problem: 

You have to make 100 cupcakes for your upcoming end of year party and you only have 3 hours to do so after oversleeping the night before. However you are in luck because ShopRite just released their Magic Flour that makes any sort of batter immediately. In order to get the Magic Flour you have to solve a puzzle where you decode the message "avxmsvyusbyxj" using the key word SIFT. Time is ticking!

*Note: wrap the result you get in the flag format in all lowercase, eg. flag{example}*

## Solution: 

Since the message is a bunch of random characters, we hope this is just a simple cipher. Let's use [Boxentriq's Cipher Identifier](https://www.boxentriq.com/code-breaking/cipher-identifier) to get some insight.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/1b392654-9b10-4ab6-b59b-bb6df8692170)

Let's see if the Bifid Cipher is any help to us.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/d624a2b5-09ff-49e7-84cc-61496c2e103c)

Darn, still a bunch of random letters. Next, the Beaufort Cipher.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/28489717-6bbf-49cf-91e7-df08f7d67eb2)

At this point I'm just thinking this is probably not the right approach. Let's see if we can get anything out of the Vigenere Cipher, else we might be out of luck.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/2f28ccdf-2721-45e4-b491-ee9c821946ae)

Huh, those actually look like real words, and they relate to cupcakes. Testing this, we find this is our flag!

## Flag:

`flag{instantbatter}`