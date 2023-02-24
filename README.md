Binary Reverse Engineering Challenges

---

**Crackme 1 Solution (crackmes.cf/users/seveb/crackme05/download/crackme05.tar.gz):**

To solve this crackme, I unzipped the archive I downloaded from the link `crackmes.cf/users/seveb/crackme05/download/crackme05.tar.gz` and the unzipped file contained 3 files, with two of them as binary files (a 32-bit and a 64-bit) and the third a readme file, containing a simple description of the challenge:.

**My solution is shown below:**
<pre><code>
#!/usr/bin/env python3
import string
import random

# Function definition to generate a random character based on a supplied condition
 def conditional_random(cond, chars):
     res = []
     for c in chars:
         if cond(c):
             res.append(c)
     return random.choice(res)

# Generate the set of possible characters as a byte array
char_options = string.ascii_lowercase + string.ascii_uppercase + string.digits + '-'
valid_chars = bytearray()
valid_chars.extend(map(ord, char_options))

# Generate a random serial that is 19 characters long
serial = [random.choice(valid_chars) for i in range(19)]

# We know that positions 4, 9, and 14 must be a '-' char...This is technically done in the cracker() stage, but we'll just do it here...
serial[4] = 0x2d
serial[9] = 0x2d
serial[14] = 0x2d

# Logic from the paper() stage
serial[8] = conditional_random(lambda x: (x ^ serial[10]) <= 9, valid_chars)
serial[5] = conditional_random(lambda x: (x ^ serial[13]) <= 9, valid_chars)
iVar1 = (serial[10] ^ serial[8]) + 0x30
iVar2 = (serial[13] ^ serial[5]) + 0x30
serial[3] = iVar1
serial[15] = iVar1
serial[0] = iVar2
serial[18] = iVar2

# Logic from the scissors() stage
serial[1] = conditional_random(lambda x: x + serial[2] > 170, valid_chars)
serial[16] = conditional_random(lambda x: x + serial[17] > 170 and serial[1] + serial[2] != x + serial[17], valid_chars)
print("".join([chr(c) for c in serial]))
</pre></code>

**print("This is the answer!")**

**How I did it using Ghidra (and any other tools you used like gdb):**

    I opened the crackme in Ghidra by first of all running the command `./Ghidra` in my terminal and then imported the 32-bit executable version with.         Having loaded the binary into Ghidra, I then proceed to look for the `main` function since it is the main entry point of any C program, After double       clicking the main function in the system tree, it then opened up a decompilation window containing C code.  
    I found the `main` function and noticed three function calls. The first one called `rock((int)argv[1])` does is call another function `bomb()` within       it if a character matches set of pattern. I can tell because most of the function body of `rock` is made up of a while loop that is iterating over the     entire serial. Also if statement is present that checks if the size of the serial is 0x13, which confirms the suspicion that our serial is going to be     19-characters long.
    The second one called `paper` function does is creating conditions that the keygen will need to take into account to pass this verification stage. I       can tell because there are 4 code paths within the `paper` function body that will result in a call to bomb().
    The third one called `scissors` function does is creating conditions that the keygen will need to take into account to pass this verification stage. I       can tell because there are 4 code paths within the `paper` function body that will result in a call to bomb().
 
    
    
    Screenshots in here would be a nice touch -- especially if something is hard to describe in words. But images don't replace the need to explain what       you did in enough detail that someone else could reproduce what you did.

 

**Crackme 1 Solution (link/to/download/location):**

To solve this crackme, you need to __________________________.

My solution is ____________________________. (If the crackme asks for a program, include your source code in a code block)

#!/usr/bin/env python3


print("This is the answer!")

How I did it using Ghidra (and any other tools you used like gdb):

    I opened the crackme in Ghidra
    I found the `main` function and noticed three function calls.
    The first one called `________` does ________. I can tell because ___________________.
    etc.
    Screenshots in here would be a nice touch -- especially if something is hard to describe in words. But images don't replace the need to explain what you did in enough detail that someone else could reproduce what you did.



Crackme 1 Solution (link/to/download/location):

To solve this crackme, you need to __________________________.

My solution is ____________________________. (If the crackme asks for a program, include your source code in a code block)

#!/usr/bin/env python3


print("This is the answer!")

How I did it using Ghidra (and any other tools you used like gdb):

    I opened the crackme in Ghidra
    I found the `main` function and noticed three function calls.
    The first one called `________` does ________. I can tell because ___________________.
    etc.
    Screenshots in here would be a nice touch -- especially if something is hard to describe in words. But images don't replace the need to explain what you did in enough detail that someone else could reproduce what you did.
