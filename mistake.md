## mistake

This one only requires reading the source code. 

```
#include <stdio.h>
#include <fcntl.h>

#define PW_LEN 10
#define XORKEY 1

void xor(char* s, int len){
        int i;
        for(i=0; i<len; i++){
                s[i] ^= XORKEY;
        }
}

int main(int argc, char* argv[]){

        int fd;
        if(fd=open("/home/mistake/password",O_RDONLY,0400) < 0){
                printf("can't open password %d\n", fd);
                return 0;
        }

        printf("do not bruteforce...\n");
        sleep(time(0)%20);

        char pw_buf[PW_LEN+1];
        int len;
        if(!(len=read(fd,pw_buf,PW_LEN) > 0)){
                printf("read error\n");
                close(fd);
                return 0;
        }

        char pw_buf2[PW_LEN+1];
        printf("input password : ");
        scanf("%10s", pw_buf2);

        // xor your input
        xor(pw_buf2, 10);

        if(!strncmp(pw_buf, pw_buf2, PW_LEN)){
                printf("Password OK\n");
                system("/bin/cat flag\n");
        }
        else{
                printf("Wrong Password\n");
        }

        close(fd);
        return 0;
}

```

Right in this code we have all the info we need to figure out how to print the flag. Our variable `fd` will be equal to however the following evaluates 
```
fd=open("/home/mistake/password",O_RDONLY,0400) < 0
```

If it were simply, `fd=open("/home/mistake/password",O_RDONLY,0400)`
we wouldn't be able to solve this challenge the same way because the open() command returns the file descriptor created by the system when we open the file in read mode which must be greater than 2 because 0, 1, and 2 are reserved for stdin, stdout, and stderr respectively. To simplify that bit of code, we can be certain that the following is true:

```
fd = 3 < 0
```

3 < 0 is most certainly not true, thus `fd = 0`. 

```
char pw_buf[PW_LEN+1];
int len;
if(!(len=read(fd,pw_buf,PW_LEN) > 0)){
		printf("read error\n");
		close(fd);
		return 0;
}
```

`pw_buf` will read 10 bytes into itself from the file descriptor at fd (which is 0, stdin). So whatever 10 character we type here will be stored ther.

```
char pw_buf2[PW_LEN+1];
printf("input password : ");
scanf("%10s", pw_buf2);
```

`pw_buf2` is filled with another 10 bytes of our input.


```
// xor your input
xor(pw_buf2, 10);

if(!strncmp(pw_buf, pw_buf2, PW_LEN)){
		printf("Password OK\n");
		system("/bin/cat flag\n");
}
```

Our input in `pw_buf2` then calls the `xor()` function which xors each byte with the value `1`. Since we need `pw_buf` and `pw_buf2` to be equal to get the flag we can simply type `0000000000` after the program tells us to not bruteforce and type `1111111111` when the program asks for the password in order to get the flag.
