# crypto/double-trouble

## Problem: 

The pros solve it by hand.

[`HSCTF_Cryptography_Challenge.pdf`](https://hsctf-10-resources.storage.googleapis.com/uploads/bb4959cab76a65fbe12a6745666cd9a185cf6a2b27ae5e69e955be7ad143e5f1/HSCTF_Cryptography_Challenge.pdf)

## Solution: 

Since we're given a definition for some kind of salad, let's search it up and figure out what salad we're working with.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/899b8d8b-5c39-4db4-a4b8-e347720cc99e)

Caesar cipher it is! Let's chuck the gibberish below the definition for [DCode](https://www.dcode.fr/caesar-cipher) to decrypt (or recognize `tzou{}` -> `flag{}` and find the shift manually).

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/6206dc76-f265-4ef8-be66-10ca29b152aa)

```
The flag is the following: AycqypAgnfcpqYpcAmmj
However, it is encoded so you have to decode it first!
```

Wow, hilarious. Luckily, we can just throw this into DCode again to Ceaesar shift once more. Take that!



## Flag:

`flag{CaesarCiphersAreCool}`