# algo/bank

## Problem: 

`nc bank.hsctf.com 1337`

[`Bank.pdf`](https://hsctf-10-resources.storage.googleapis.com/uploads/e897a468fea4fcaa4b7bb12e5ff30f3cfe3668e43e494519fb44a9f30f345c9b/Bank.pdf)

## Solution: 

LETS GO I LOVE ROBBING BANKS!!!!!!!

Anywho, let's figure out how we should approach this. We're told each account will only be open for at most 20 minutes, but we only need 10 minutes to hack an account. To effectively do this and avoid any edge cases, let's sort the windows based on how early they *end* rather than begin, and loop through greedily (this idea is actually outlined in [USACO Guide](https://usaco.guide/silver/greedy-sorting?lang=cpp)).

```Python
from pwn import *
io = remote("bank.hsctf.com", 1337) # use pwntools for input/output

for _ in range(5): # loops through each test case
    x = io.recvline().decode() # number of windows
    print(x)
    N = int(x)
    accs = []
    for _ in range(N):
        a,b = map(int, io.recvline().split())
        print(a, b)
        if b - a < 10:
            continue # if the window isn't even open for 10 minutes, why bother with it??
        accs.append((b, a)) # store by ending ('b') rather than beginning ('a')
    accs.sort() # sort by ending
    ans = 0
    c = accs[0][1] # start at the beginning of the window that closes earliest
    for i in range(N):
        if accs[i][0] - c >= 10: # if we can make the window, rob this account
            ans += 1
            c = max(accs[i][1] + 10, c + 10) # update current time
    print(ans)
    io.send(str(ans) + '\n') # sends output to the netcat

print(io.recvline().decode()) # receives the flag
```

## Flag:

`flag{gr33dy_4lg0r1thm_048cc38517}`