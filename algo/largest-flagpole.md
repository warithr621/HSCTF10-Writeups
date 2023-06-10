# algo/largest-flagpole

## Problem: 

`nc largest-flagpole.hsctf.com 1337`

Clarification: the flagpole must be the full component, not just a subset of the nodes of a component

[`largest-flagpole.pdf`](https://hsctf-10-resources.storage.googleapis.com/uploads/f1650b29c45dc27366182907fadb8fdc28d5bc11ed40ce273d6d942dd65bd014/largest-flagpole.pdf)

## Solution: 

As a warning, this is probably the most convoluted code I've ever written (and most likely unnecessary), there's definitely several things I could've cut out from my solution since they were taken care of later).

### Analyzing the problem and finding a solution

We need to first separate our graph into components, or parts of the graph such that no two parts are connected with each other. Once we do this, we need to find some way to identify if a graph is a legal flagpole. We can do this the following way:
- First, we can skip any graph with less than 5 nodes, since this is the minimum number of nodes necessary for a legal flagpole (check image below)

![image](https://github.com/warithr621/HSCTF10-Writeups/assets/64328893/8ab9a24f-47f3-48b4-89a6-542cecc697c1)
- Next, we need to locate the flag part of the flagpole. We can use a DFS (depth-first search) approach to find all cycles in the graph (although if there's more than one it can't be a flagpole), and confirm this cycle has size divisible by 4 (by definition of a simple cycle, if the number of edges is divisible by 4, the number of constituent nodes is also divisible by 4).
- Once we locate this flag, we need to search for the pole part. We can start by searching for a node with degree 3 in the cycle, since this node would connect to two other nodes in the cycle and a node branching out to the pole (if multiple of such nodes exist, the best case scenario is the flagpole has another part added onto it, making the component invalid).
- After such a node is located, we need to travel across the node (making sure not to backtrack into the cycle again!) and make sure this is indeed a pole (i.e. it doesn't branch off and ends at a node of degree 1). There is a possibility that this could cycle back, but this is already taken care of earlier by confirming the component doesn't have multiple cycles.

### Writing a pwn script

To gather all the data to store it conveniently, we can use Python's `pwn` module to take input from the netcat and store it in a separate file.

```Python
from pwn import *
import time

io = remote("largest-flagpole.hsctf.com", 1337) # sets up pwntools
f = open("data.txt", "w") # file writer

T = io.recvline().decode() # number of test cases
f.write(T)
for _ in range(int(T)):
	S = io.recvline().decode() # number of nodes + number of edges
	f.write(S)
	M = S.split()[1] # number of edges
	for tmp in range(int(M)):
		f.write(io.recvline().decode())

print(io.recvline().decode()) # the line that says "Output:"

f.close()

print("data collection complete")
time.sleep(10) # gives time to run Java code and get output
print("time to send over some data")
with open("out.txt", "r") as r:
	tmp = r.readlines()
	ans = ''.join(map(str, tmp)) 
	print(ans)
	io.send(ans) # sends over the answer
	print(io.recvline().decode())
	print(io.recvline().decode()) # helps print the flag
```

Ideally what this code does is gather the input based on how it's formatted (each test case lists the number of nodes + edges before listing all connections), stores it in a file called `data.txt`, waits 10 seconds for the Java program to run (explained later), and then reads the Java output from the file `out.txt` and sends it to the netcat.

### Solving the actual problem in Java

For reference my program is 200+ lines (oops), so you can view the code [here](https://github.com/warithr621/Comp-Programming/blob/main/Other_Contests/HSCTF/HSCTF10/LargestFlagpoleSolver.java). Here's the idea behind it...
- For each test case, reset all the old variables before running the `solve()` method
- First, set up the graph by using `parseInput()` to collect all the input
- Next, we use `findComponents()`, which uses Java's `Queue` interface in a BFS-style approach in searching the graph. All the nodes are separated into components in the array `comps`, and the array `cnum` matches each node with what component it belongs to (this way, you can find all the nodes in Component 1, but also what component Node 1 is in)
- After this, we begin testing each component
  - First of all, skip over the component if its size isn't at least 5 nodes
  - After this confirmation, we'll use the `dfs()` method to search for any cycles, which uses `cyc` to store the current cycle and `cycles` to store all found cycles
  - Once this is complete and the unique cycle (granted there is one) is found, we store it in the ArrayList `flag` and confirm its size is divisible by 4 (definition of a flag)
  - To find the pole, we try to find a node in the cycle with degree 3 (any node in the cycle should normally have degree 2, but a degree 3 would indicate a potential pole being attached), which we'll call `deg3node`
  - Once we find this node and confirm only one node exists, we try looking for the pole itself
    - We start by finding the next part of this pole, aka the node connected to `deg3node` that isn't in the cycle itself
    - We then set up a system of monitoring the current and previous node (`curnode` and `prev`) and going down the pole while the degree of `curnode` isn't 1, confirming along the way that each new instance of `curnode` has degree 2 and has a new connection that doesn't backtrack (i.e. not equal to `prev`)
  - Assuming we've indeed found no errors by then, we calclulate the size of the flagpole using the given formula (if the flag has $F$ nodes and the pole $P$ nodes, the size is $F^2+P$), and updating our maximum size
- Once all nodes are checked, we output this answer to the file `out.txt` and move on to the next test case

Once this is all good we can solve the problem: run the pwn script, wait for confirmation all input is stored, run the Java program, and once `time.sleep(10)` concludes the output will be sent and the flag will be returned.

## Flag:

`flag{l4rge_fl4g_4738}`