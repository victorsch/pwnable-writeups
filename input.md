## Input

This one was tougher as I was for a while looking at how to do this the normal way via GDB and some exploit, but I was looking too far into it as some writeups online pointed me in the direction of just giving the code what the source says it wants.

Trying to do this via a python one liner or bash wasn't working because it required sending a null byte (\x00), which wasn't quite working. The solution is to use pwntools to pass all the stages, and it turns out this challenge is good for learning the ins and outs of pwntools.

Here's my solve script:

```
from pwn import *
import os

args = []

for x in range(0, 100):
        args.append("A")

# Stage 1
args[65] = "\x00"
args[66] = "\x20\x0a\x0d"

# Stage 2
r1, w1 = os.pipe() # Returns a pair of file descriptors for read and write, to be written as stdin
r2, w2 = os.pipe() # to be used as stderr where stage 2 needs to read from fd 2
os.write(w1, '\x00\x0a\x00\xff') # write to a file descriptor
os.write(w2, '\x00\x0a\x02\xff')

# Stage 3
env_var = {'\xde\xad\xbe\xef': '\xca\xfe\xba\xbe'}

# Stage 4
with open("\x0a", "w") as f:
        f.write('\x00\x00\x00\x00')
        f.close()

# Stage 5
args[67] = '1337'

p = process(executable = '/home/input2/input',
                argv=args,
                stdin=r1, stderr=r2,
                env=env_var)

r = remote('localhost', 1337)
r.sendline('\xde\xad\xbe\xef')

p.interactive()
```

For Stage 1, you need to make whatever is at args['A'] equal to the null byte \x00 and at args['B'] the other series of bytes.

For Stage 2, you need to overwrite the file descriptors with what the program requests. Using os.pipe() we can grab a read, write file descriptor pair that we can use to pass into the process that will be run by pwntools.

For Stage 3, we just need to set an environment variable which can be done via a dictionary.

For Stage 4, we just need to write the requested text to the given file name.

For Stage 5, we need to connect to a socket and send a line with the requested text.
