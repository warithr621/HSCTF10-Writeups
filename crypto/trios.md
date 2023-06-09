# crypto/trios

## Problem: 

what number is a trio?

[`trios.py`](https://hsctf-10-resources.storage.googleapis.com/uploads/d7476fa8c0bff6df5bf4c7b5e8f904b3a494742e91812f90e87999c9185cb7d6/trios.py) [`data.txt`](https://hsctf-10-resources.storage.googleapis.com/uploads/b146449ba52a8c6b751004dd366540893b03ea6db98b13261c425a730998aa8c/data.txt)

## Solution: 

So we have a Python program and its output of absolute nonsense...great!

Analyzing the code, we can understand the basic idea of the program
- for each letter of the alphabet, it generates a random 3-character sequence (of chars and/or digits) to match to that letter
- it then iterates through the flag (`msg`) and for each letter it appends it to `encoded`, which is outputted

The message seems scary at first, but what's good is the quick insight we get: the beginning of this output looks like the flag, so the first 12 characters must pair up with the letters in flag
- f -> `8wn`
- l -> `APR`
- a -> `2sv`
- g -> `yje`

Using a find-and-replace tool, we slowly start eliminating letters (bolded letters have been converted).

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/2ec1ef65-bf85-4b48-8298-568aa796c32e)

We now have to find a way to start slowly eliminating letters, which was a pain to start listing.
- **a**phj is one of the words, so `phj` must be a letter 'x' such that **a**'x' is a valid word (a*m*, a*n*, a*s*, and a*t* are all examples)
- uZb**f** is also a word, so `uZb` must make 'x'**f** a valid word (*i*f and *o*f are the only realistic options)
- tKO**ll**Rbc**gal** was a word, so we must find 'x' such that a 7-letter word could be formed (illegal is the only possible option, meaning...)
  - i -> `tKO`
  - e -> `Rbc`
  - o -> `uZb`

Now for some more substitution:

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/e3f4d31a-2a23-471d-a2b7-8f89150328f6)

- **o**phj**e** is a word, so "n -> `phj`" is most likely correct
- **eno**4zD**g**wDE is a word, and the only 6-letter word that exists is enough
  - u -> `4zD` (all vowels found!)
  - h -> `wDE`

By now, we get the flag text as **ele**zwt**han**Iqr. The only matches that make a valid 8-letter word are "p -> `zwt`" and "t -> `Iqr`". Making these final substitutions, we get:

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/5df5fae4-585d-4f3c-8603-b69f866c8961)

## Flag:

`flag{elephant}`

### P.S.

Afterwards, I continued trying to find the rest of the alphabet to decrypt the full message, which you can view [here](https://docs.google.com/document/d/1-GBVj2qAhMem2Zf9NmAln5HAClYEF1siU_g-yQxm9IU/edit).

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/7d754882-c1a9-4d04-963d-a79a7bca8912)

It turns out that since guinea pigs need social interaction to maintain social health, it's illegal by Swiss law to have *only* one guinea pig. Welcome to HSCTF: where you solve challenges and learn about guinea pigs!