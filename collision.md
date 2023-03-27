Our goal is to get our input to be equal to 20 bytes in length, and also equal the hashcode that's hardcoded into the program. This isn't really so much of an MD5 collision as much as it is an algebra problem.

```
#include <stdio.h>
#include <string.h>
unsigned long hashcode = 0x21DD09EC;
unsigned long check_password(const char* p){
        int* ip = (int*)p;
        int i;
        int res=0;
        for(i=0; i<5; i++){
                res += ip[i];
        }
        return res;
}

int main(int argc, char* argv[]){
        if(argc<2){
                printf("usage : %s [passcode]\n", argv[0]);
                return 0;
        }
        if(strlen(argv[1]) != 20){
                printf("passcode length should be 20 bytes\n");
                return 0;
        }

        if(hashcode == check_password( argv[1] )){
                system("/bin/cat flag");
                return 0;
        }
        else
                printf("wrong passcode.\n");
        return 0;
}

```

0x21DD09EC in decimal is 568134124. If we divide that by 5 (the number of times the loop goes in check_password), we get 113626824. We can represent that in byte format to the program 4 times, and present the remainder in bytes the fifth time to pass this check.

```
python

print(__import__("struct").pack("<i", 113626824) * 4 + __import__("struct").pack("<i", 113626828))'
b'\xc8\xce\xc5\x06\xc8\xce\xc5\x06\xc8\xce\xc5\x06\xc8\xce\xc5\x06\xcc\xce\xc5\x06'
```

That string is what needs to get passed to the program, but in a special way. The "<" in our struct format string means to print in little endian in format, and the "i" lets it know to expect an integer.
The __imp

If we just pass this to the program like this it won't work right.
```
./col `echo \xc8\xce\xc5\x06\xc8\xce\xc5\x06\xc8\xce\xc5\x06\xc8\xce\xc5\x06\xcc\xce\xc5\x06`
```

You need to use -n and -e so that echo doesn't send a newline to the program, and so it interprets the backslashes as escapes and doesn't just send all that data as a pure string. The backticks are necessary so that the echo gets executed before the ./col

Here is the correct command:

```
./col `echo -n -e \xc8\xce\xc5\x06\xc8\xce\xc5\x06\xc8\xce\xc5\x06\xc8\xce\xc5\x06\xcc\xce\xc5\x06`
daddy! I just managed to create a hash collision :)

```
