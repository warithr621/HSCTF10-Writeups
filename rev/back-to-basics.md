# rev/back-to-basics

## Problem: 

Try to solve it with your eyes closed

[`ReverseEngineeringChallenge.java`](https://hsctf-10-resources.storage.googleapis.com/uploads/c2268f4bede316605d00c9e649250ffcfac876d7259c9caf7df35d7549036b81/ReverseEngineeringChallenge.java)

## Solution: 

Reading the Java file, we see that the user has to input a String that's stored as `userInput` in line 7, which is immediately used in the `checkPassword()` method. Reading the contents of this method gives us some insight on the flag.

- `checkPassword()` returns a boolean value, which we can assume is true if the passed argument is the flag for this challenge
- One of the requirements is that the flag is 20 characters
- The next several lines each check a specific (0-indexed) character and confirm it matches the actual

To solve this, we can slowly map out which character should be which, and our flag should appear in plain sight.
```
f l a g { c 0 d 1 n g  _  i  5  _  h  4  r  d  }
0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19
```

## Flag:

`flag{c0d1ng_i5_h4rd}`