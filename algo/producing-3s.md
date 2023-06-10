# algo/producing-3s

## Problem: 

`nc producing-3s.hsctf.com 1337`

[`producing-3s.pdf`](https://hsctf-10-resources.storage.googleapis.com/uploads/404c0962d973ad42710830bd725c227410a847d8b1585fd0ed42ed5c1bac97dd/producing-3s.pdf)

## Solution: 

Let's use some Number Theory to figure this out. For each element, we want to see if it either already ends in 3, or we can multiply it by some group of numbers that come before it so that it does. Since we only care about the last digit, let's only store the last digit for all the numbers (for each `a`, store `a % 10` rather than `a`). Now let's loop through each element from left to right.
- We're going to keep track of a list called `YES`, such that if `YES[i]` is True, we can get some subset of the previous elements to multiply to a number ending in `i` (since keeping the number is the same as multiplying it by 1, `YES[1] = True` for all elements
- Firstly, let's go through each of the 10 possible digits (0-9) and check to see if we can form it and get a product ending in 3 (`YES[digit] == True and digit * cur_element % 10 == 3`)
- Once we verify that this either can or cannot happen, we need to update `YES` for the next element
- We'll do this by maintaining a new list `C` that copies from `YES`, and additionally adding any new digits we can form by multiplying previous ones by the current element
- Once this is all done, we'll keep track of a string that adds `Y` or `N` according to the problem's requirement, and set `YES[cur_element]` to True in case it already hasn't been
- Lastly once all test cases are done, we'll just gather the flag from the system

```Python
from pwn import * # pwntools for I/O
import json
import time

io = remote("producing-3s.hsctf.com", 1337)

T = int(io.recvline().decode()) # number of test cases
ans = ""
for _ in range(T):
    N = int(io.recvline().decode()) # number of elements
    tmp = io.recvline().split()
    A = [] # array of elements
    for x in tmp:
        A.append(abs(int(x)) % 10) # only the last digit  matters
    YES = [False for i in range(10)]
    YES[1] = True
    for i in range(N):
        ok = False
        for j in range(10):
            if YES[j] and j * A[i] % 10 == 3:
                    ok = True
        C = [i for i in YES]
        for j in range(10):
            if YES[j]:
                C[j * A[i] % 10] = True
        YES = C
        ans += ("Y" if ok else "N")
        YES[A[i]] = True
    ans += '\n'

    print("{0:0.2f}% done".format((_+1) / T * 100)) # progress indicator

io.send(ans.strip()+'\n')

print(io.recvline().decode())
print(io.recvline().decode())
print(io.recvline().decode())
```

## Flag:

`flag{n1c3_w0rk_8329}`