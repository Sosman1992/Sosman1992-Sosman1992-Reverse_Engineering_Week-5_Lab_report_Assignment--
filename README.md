Binary Reverse Engineering Challenges

---

**Crackme 1 Solution (crackmes.cf/users/seveb/crackme05/download/crackme05.tar.gz):**

To solve this crackme, I unzipped the archive I downloaded from the link `crackmes.cf/users/seveb/crackme05/download/crackme05.tar.gz` and the unzipped file contained 3 files, with two of them as binary files (a 32-bit and a 64-bit) and the third a readme file, containing a simple description of the challenge:.

**My solution is shown below:**
<pre><code>
#!/usr/bin/env python3
import string
import random

"""Function definition to generate a random character based on a supplied condition"""
 def conditional_random(cond, chars):
     res = []
     for c in chars:
         if cond(c):
             res.append(c)
     return random.choice(res)

"""Generate the set of possible characters as a byte array"""
char_options = string.ascii_lowercase + string.ascii_uppercase + string.digits + '-'
valid_chars = bytearray()
valid_chars.extend(map(ord, char_options))

"""Generating a random serial that is 19 characters long"""
serial = [random.choice(valid_chars) for i in range(19)]

serial[4] = 0x2d
serial[9] = 0x2d
serial[14] = 0x2d

""" paper function Logic """
serial[8] = conditional_random(lambda x: (x ^ serial[10]) <= 9, valid_chars)
serial[5] = conditional_random(lambda x: (x ^ serial[13]) <= 9, valid_chars)
iVar1 = (serial[10] ^ serial[8]) + 0x30
iVar2 = (serial[13] ^ serial[5]) + 0x30
serial[3] = iVar1
serial[15] = iVar1
serial[0] = iVar2
serial[18] = iVar2

"""scissors function logic"""
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
    
![key](https://user-images.githubusercontent.com/66968869/221465037-9516172e-beac-48a1-a04c-72cf51ea182a.png)


**Crackme 1 Solution (crackmes.cf/users/adamziaja/crackme1/download/crackme1.tar.gz):**

To solve this crackme, I unzipped the archive I downloaded from the link `crackmes.cf/users/adamziaja/crackme1/download/crackme1.tar.gz`

**My solution is shown below:** 
<pre><code>
import argparse

def generate_key(username):
    # Make sure the username is between 8 and 12 characters long
    if len(username) < 8 or len(username) > 12:
        print("Username must be between 8 and 12 characters.")
        return None

    key = ""
    for i, c in enumerate(username):
        if i % 2 == 0:
            key += str(ord(c.lower()))
        else:
            key += str(ord(c.upper()))

    key = (key[2*(len(username)-8):][0:8])
    return int(key)

if __name__ == "__main__":
    parser = argparse.ArgumentParser("crackme1 keygen")
    parser.add_argument("username")
    args = parser.parse_args()
    username = args.username

    key = generate_key(username)
    if key is not None:
        print(f"The key for username {username} is: {key}.")
</pre></code>

print("This is the answer!")

How I did it using Ghidra (and any other tools you used like gdb):

    I opened the crackme in Ghidra and I found the `main` function with a return type of int and no parameters.
    The function starts by declaring and initializing several variables of different data types, including bool, 
    int, basic_ostream, ulong, char, string, double, istringstream, and stringstream. It also declares an array 
    of basic_ostream objects named abStack_178 and also two objects of type "istringstream" and "stringstream", 
    which are used for input and output.

    The function then uses several C++ standard library functions and operators to perform input/output operations
    and string manipulation. It prompts the     user to enter a username and a serial number, reads the input from
    the standard input stream (cin), and validates the input. If the input is valid, it performs some string manipulation 
    on the username and serial number, and prints the result to the standard output stream (cout).

    The program has function calls to various C++ standard library functions, such as std::getline, std::tolower, std::toupper,
    std::operator<<, std::operator>>, and several others. In addition, there are also some constructor and destructor calls for 
    objects of C++ standard library classes like std::basic_stringstream and std::basic_string.
     
    Screenshots in here would be a nice touch -- especially if something is hard to describe in words. But images don't 
    replace the need to explain what you did in enough detail that someone else could reproduce what you did.

![sno](https://user-images.githubusercontent.com/66968869/221465429-e4fc1f70-23d2-4664-a7b6-e2ba9ac600c8.png)

Crackme 1 Solution (link/to/download/location):

To solve this crackme, I unzipped the archive I downloaded from the link `crackmes.cf/users/crackmes-de-hmx0101s-decryptme-1` and opened it in windows XP
which requires a code that can provide me with a key to apply to the text box of launched crackme executable in the windows environment to decrypt an unclear message whenever a wrong key is typed into the textbox. 

**My solution is shown below:**
<pre><code>
#!/usr/bin/env python3

</pre></code>

print("The key is 254")

How I did it using Ghidra (and any other tools you used like gdb):

    I opened the crackme in Ghidra I found the `main` function and double clicked to launch it in the
    Decompile window pane and I found some function calls within it. The first one called `MessageBoxA`
    and it takes 4 parameters. The MessageBoxA function is called only when a conditional check is done 
    to ascertain whether a null character is present in the memory location DAT_00015018. If the null 
    character is present, it means that the string stored in that memory location has ended, and hence 
    it is a valid string else the if there is an abscence of a null character in the memory location 
    it means that the string is not properly terminated, and there could be a runtime error as a result 
    of accessing memory beyond the end of the string. In this case, the code displays an error message
    box with the message "s_Runtime_error_at_00000000_00015054" and the title "s_Error_0001504c".
    
    
