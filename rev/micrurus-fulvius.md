# rev/micrurus-fulvius

## Problem: 

*Micrurus fulvius*, commonly known as the eastern coral snake, common coral snake, American cobra, and more, is a species of highly venomous coral snake in the family Elapidae. The species is endemic to the southeastern United States. It should not be confused with the scarlet snake (*Cemophora coccinea*) or scarlet kingsnake (*Lampropeltis elapsoides*), which are harmless mimics. No subspecies are currently recognized.

[`micrurus-fulvius.pyc`](https://hsctf-10-resources.storage.googleapis.com/uploads/c86c0946f48499bc7e85bafc4daaac95031655f3b9685e907afd57608997cfaa/micrurus-fulvius.pyc)

## Solution: 

Wait what's a `.pyc` file?
![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/2fc4bb64-df8c-45a5-9f4a-898379577e80)

Oh ok, let's see if we can decompile this using `decompyle3`.

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/e4a41944-ade5-40ba-961b-36385f403b1b)

### `.pyc` Analysis

Now what the hell is going on here...time to do some Python analysis again!!!!!!!!!!! I love Python.
- Uses the `t()` method to check whether the input (var `x`) is valid
- Checks if the length of `x` is 1 more than the length of the list `l`
- Loops through the string `x`, storing `c` as a pair of characters and `i` as index (for instance in "bark", `i=0 -> c="ba"`, `i=1 -> c="ar"`, etc.)
  - Runs `j()` on the two characters, which uses their ASCII values and returns an integer
  - Subtracts 10 from this value, and then runs `a()` to calculate how many steps it takes to reach 1 according to the [Collatz Conjecture](https://en.wikipedia.org/wiki/Collatz_conjecture)
  - Passes this value into `d()` alongside the index, which returns another value thats tripled and compared to `l[i]`
- Once this is all done and no falsities are found, it checks if `k(x.encode()).hexdigest()` starts with `b4f9d505`, and if true the method `t()` returns True (the input `x` is the flag!)

### Reversing the `.pyc` code

Now that we know what everything does, let's reverse this code! My program for this is [here](https://github.com/warithr621/Comp-Programming/blob/main/Other_Contests/HSCTF/HSCTF10/MicrurusFulviusSolnPartOne.py), but I'll walk through all my steps below:
- We start with trying to make `d( a( j(*c) - 10 ), i ) * 3 == l[i]` a true statement
- If we divide each side by 3 and reverse the method `d()`, we can get a value equivalent to `a(j(*c)-10)`
  - Set `cur = l[i] // 3` as in line 36 of my program
  - If `d(u,p) = (u << p % 5) - 158`, then `u = ( d(u,p) + 158 ) >> (p % 5)`, where at this instant `cur = d(u,p)` and `u = a(j(*c)-10)`
- By now, we don't have any really efficient way of converting this value into a 2-letter sequence `*c` (or at least one I could find in-contest), so we'll need to do some precomputation...
  - Lines 5-8 store all possible 2-character combinations (according to the alphabet outlined in `characters`) in a variable `possible_strings`
  - The method `collatz()` is used to calculate the value of `a(n)` for some arbitrary `n`, using tabulation to reduce unnecessary calls (storing already-calculated values in a dict `a`)
  - Once this is all done, we loop through all possible strings in `possible_strings` and check if `a(j(*c)-10)` is the value stored under `cur` (as in lines 41-47), appending all strings that work in the variable `works` and then printing it

### A teeny tiny problem

I thought this would be enough to get the unique 2-letter combination for each pair to get the flag, but the problem is that there *wasn't* a unique solution. For instance, the first value of `l[i]` (-153) has the following `*c` pairs that work:
```
['aD', 'aK', 'aT', 'a{', 'bt', 'bv', 'bx', 'bF', 'bW', 'b}', 'cu', 'cv', 'cI', 'cP', 'c9', 'dr', 'dw', 'd|', 'ek', 'em', 'eF', 'eH', 'eP', 'e6', 'fe', 'fl', 'fq', 'f0', 'gb', 'gE', 'ha', 'he', 'hf', 'iQ', 'iW', 'i2', 'i.', 'jL', 'jV', 'jZ', 'j!', 'kF', 'lF', 'lG', 'lM', 'lP', 'lQ', 'n7', 'n9', 'n<', 'p1', 'p7', 'p9', 'p.', 'ry', 'r&', 'r.', 's$', 's*', 't$', 'Yw', 'Zq', '_O', '_V', '__', '^U']
```

Yeah, this might be a problem... how in the world are we gonna figure out what should match to what??

### A not so teeny tiny solution

To fix this slight problem, we're gonna do this in the most competitive-programming way possible (at least to my very limited extent): BFS.

BFS stands for Breadth-First Search, which starts at the root of a node and searches each level one at a time. The key that makes this (or DFS) feasible is that if the program is built on consecutive characters, they must be linked. For instance we know that we're trying to find the flag that starts with `flag{`, so the first pair must be `'fl'`, then `'la'`, then `'ag'`, then `'g{'`, and so on. By searching our lists like this, we can find out exactly what our flag is. Now, it's time to walk through my code [here](https://github.com/warithr621/Comp-Programming/blob/main/Other_Contests/HSCTF/HSCTF10/MicrurusFulviusSolnPartTwo.java).
- Lines 7-18 parse all of the input printed by the Python program I wrote earlier, storing each list of possible charactrs under a different "level" (ideally based on the index `i` from the `.pyc` file)
- Lines 19-25 builds a `Queue` that starts keeping track of our input, which looks for all the pairs in index 4 that start with `{` (since `i=0 -> 'fl'`, `i=1 -> 'la'`, `i=2 -> 'ag'`, `i=3 -> 'g{'`, `i=4 -> '{?'`)
- Line 27 begins the BFS, which works as so:
  - First, we pop an element from the `Queue` which stores the current character-pair and the next index we should be looking at
  - We then loop through this index to search for any possible options we can continue the flag with (if our current character-pair is `e1` for example, we want to look for pairs that begin with `1` now)
  - If we find one, we push it into the `Queue` with an incremented index to look for
  - If we're already at the last index and the final string ends in `}`, we've found our flag (!!) so now we can just print it

That was a pretty rough explanation, but the point is once this is run we get the following output:
```
flag{380822974}
flag{3808*0<24}
flag{380922974}
```

### Cleaning things up

Wait why are there multiple outputs... I'll be honest with you, I just picked the first flag I saw and it happened to be right. Here's the actual idea to finalize this: we can now use the `.pyc` requirement to test which is our flag: `k(x.encode()).hexdigest()[:8] == "b4f9d505"` must be true.

```Python
from hashlib import sha256 as k

poss = ["flag{380822974}", "flag{3808*0<24}", "flag{380922974}"]
for x in poss:
    if k(x.encode()).hexdigest()[:8] == "b4f9d505":
        print(f"We found it!! The flag is {x}")
```

```
We found it!! The flag is flag{380822974}
```

Finally.

## Flag:

`flag{380822974}`
