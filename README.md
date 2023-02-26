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

**print("This is the answer 5jp6-K-uk-moHN-6Ms5!")**

**How I did it using Ghidra (and any other tools you used like gdb):**

    I opened the crackme in Ghidra by first of all running the command `./Ghidra` in my terminal and then imported the 32-bit
    executable version with. Having loaded the binary into Ghidra, I then proceed to look for the `main` function since it is
    the main entry point of any C program, After double clicking the main function in the system tree, a decompilation window 
    containing C code was opened.  I found the `main` function and noticed three function calls. 
    
    The first one called `rock()` takes an integer argument called `serial`. Also includes a function called `bomb` that may
    cause the program to terminate abruptly. The rock function validates `serial` an input string to rock to ensure that the 
    string meets certain requisite by checking If a character is not a digit or a letter, or it is a letter outside the range 
    A-Z or a-z, the function calls "bomb" and terminates the program or If a character is a letter between A-Z/a-z, prints 
    "ROCK 2: [index] - [character]" to the console and calls the "bomb" function or If a character is a letter between '-' 
    and '0' (inclusive), or a letter between ':' and '@' (inclusive), the function prints "ROCK 1:[index]-[character]" 
    to the console and calls the "bomb" function or If the input string has a length different from 19 characters, it
    prints "ROCK 4: Serial not 19 chars!" to the console and calls the "bomb" function. However If the input string passes 
    all of these checks, the function returns without doing anything else after the `rock` function iterates over the input 
    characters of the string using a while loop and performing of several checks on each of the entered character.
    I can tell because  the presence of the `bomb` function and the use of the `printf` and `puts` functions suggest that 
    the program is intended to provide some kind of interactive console interface or output. Additionally, the "while" loop 
    with an "if-else" statement nested inside it suggests that the function is iterating over a string input and performing 
    some validation checks.
    
    The second one called `paper` function and its purpose is to validate the input string `key` to ensure that it meets 
    certain requirements. In additon, the function performs several operations on an input string, comparing the results 
    of the string to specific values, and then returns without doing anything else if the input string is valid.
    I can tell this because, the presence of the variable, conditional statements, bomb function, indicates that the function 
    `paper` is performing some kind of validation or verification of input data using bitwise XOR and integer arithmetic. 
    Also the use of `puts` function to print messages to the console and the conditional execution of the "bomb" function 
    indicate that the program may have some interactive or error-handling features.
    
    The third one called `scissors` performs some sort of check on the serial parameter, and calls `bomb` if certain conditions 
    are not met by validating certain bytes in the serial argument and terminates the program if the validation fails.
    I can tell this because, the scissors function takes an integer argument serial as its parameter, which is interpreted as 
    a memory address, and performs some operations on the contents of memory at that address.

    The fourth function called `cracker` calculates the sum of the integer values of the characters at the memory locations 
    `serial+0xe`,`serial+4`, and `serial+9`. If the sum is not equal to 0x87 (which is decimal 135), the function prints an 
    error message `cracker 1` and calls the `bomb` function to terminate the program. However, if the sum is equal to 0x87, 
    the function returns without doing anything else. I can tell this because, the function accesses the value of a character 
    at a specific memory location by dereferencing a pointer to that location.
    
    
    Screenshots in here would be a nice touch -- especially if something is hard to describe in words. But images don't replace
    the need to explain what you did in enough detail that someone else could reproduce what you did.

 

**Crackme 1 Solution (crackmes.cf/users/adamziaja/crackme1/download/crackme1.tar.gz):**

To solve this crackme, you need to __________________________.

My solution is ____________________________. (If the crackme asks for a program, include your source code in a code block)

#!/usr/bin/env python3

def generate_key(username):
    if not 8 <= len(username) <= 12:
        print("username must be between 8 and 12 characters.")
        quit()
    key = '' 
    for i, u in enumerate(username):
        if i%2:
            key += str(ord(u.upper()))
        else:
            key += str(ord(u.lower()))
    return int(key[2*(len(username)-8):][0:8])

if __name__=="__main__":
    parser = argparse.ArgumentParser("crackme1 keygen")
    parser.add_argument("username")
    args = parser.parse_args()
    print(generate_key(args.username))
    
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
