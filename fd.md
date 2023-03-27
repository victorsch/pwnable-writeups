## fd

This challenge has an easy hint in it since it tells you to look at file descriptors. The C function read(x, y, z) will read z amount of bytes into the buffer y from the file descriptor at x.

The source code tells us that the first argument has 0x1234 subtracted from it which is then used as the file descriptor. Since we want to type our own input, we want fd to be 0. 0x1234 is 4660 so let's use that as the first parameter.

```
int fd = atoi( argv[1] ) - 0x1234;
        int len = 0;
        len = read(fd, buf, 32);
        if(!strcmp("LETMEWIN\n", buf)){
                printf("good job :)\n");
                system("/bin/cat flag");
                exit(0);
        }
```

```
./fd 4660
LETMEWIN
mommy! I think I know what a file descriptor is!!
```

Nice.
