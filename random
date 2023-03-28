The "random" library is not so random without a seed, and if you run the program multiple times you'll notice it's the same value everytime.

The "random" value is being stored in $rbp-0x4 per the assembly code here, and we can verify it on multiple runs to verify it never changes and is always the same

Per the source code, we know that our input needs to XOR with that random value to equal 0xdeadbeef.

```
   0x00000000004005f4 <+0>:     push   %rbp
   0x00000000004005f5 <+1>:     mov    %rsp,%rbp
   0x00000000004005f8 <+4>:     sub    $0x10,%rsp
   0x00000000004005fc <+8>:     mov    $0x0,%eax
   0x0000000000400601 <+13>:    callq  0x400500 <rand@plt>
   0x0000000000400606 <+18>:    mov    %eax,-0x4(%rbp)
   0x0000000000400609 <+21>:    movl   $0x0,-0x8(%rbp)
   0x0000000000400610 <+28>:    mov    $0x400760,%eax
   0x0000000000400615 <+33>:    lea    -0x8(%rbp),%rdx
   0x0000000000400619 <+37>:    mov    %rdx,%rsi
   0x000000000040061c <+40>:    mov    %rax,%rdi
   0x000000000040061f <+43>:    mov    $0x0,%eax
   0x0000000000400624 <+48>:    callq  0x4004f0 <__isoc99_scanf@plt>
   0x0000000000400629 <+53>:    mov    -0x8(%rbp),%eax
   0x000000000040062c <+56>:    xor    -0x4(%rbp),%eax
   0x000000000040062f <+59>:    cmp    $0xdeadbeef,%eax
   
   x/x $rbp-0x4
   0x6b8b4567
```

I found an XOR calculator online which tells us the decimal value that XORs with 0x6b8b4567 to get 0xdeadbeef is 3039230856. Simply typing this into the program gets us our flag:

```
random@pwnable:~$ ./random 
3039230856
Good!
Mommy, I thought libc random is unpredictable...
```
