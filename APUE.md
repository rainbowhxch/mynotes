## APUE NOTE

### Error Option

```c
#include <string.h>
char *strerror(int errnum)
```

```c
#include <stdio.h>
void perror(const char *msg)
```

### File Operation

```c
#include <fcntl.h>
int open(const char *path, int Oflag, .../* mode_t mode */)
int openat(int fd, const char *path, int Oflag, .../* mode_t mode */)
where 'Oflag' is: O_RDYNLY O_WRONLY O_RDWR O_EXEC O_SEARCH O_APPEND
O_CLOEXEC O_CREAT O_NOCTTY O_NOFOLLOW O_NONBLOCK O_SYNC O_TRUNC O_TTY_INIT
O_DSYNC O_RSYNC
```

```c
#includs <fcntl.h>
int creat(const char *path, mode_t mode)
```

```c
#include <unistd.h>
int close(int fd)
```

```c
#include <unistd.h>
off_t lseek(int fd, off_t offset, int whence)
where 'whence' is SEEK_SET SEEK_CUR SEEK_END
```

```c
#include <unistd.h>
ssize_t read(int fd, void *buf, size_t nbytes)
ssize_t write(int fd, void *buf, size_t nbytes)
```

```c
#include <unistd.h>
ssize_t pread(int fd, void *buf, size_t nbytes)
ssize_t pwrite(int fd, void *buf, size_t nbytes)
```

```c
#include <unistd.h>
int dup(int fd)
int dup2(int fd, int fd2)
```

```c
#include <unistd.h>
int fsync(int fd)
int fdatasync(int fd)
void sync(void)
```

```c
#include <fcntl.h>
int fcntl(int fd, int cmd, .../* int arg */)
where 'cmd' is
F_DUPFD F_DUPFD_CLOEXEC
F_GETFD F_SETFD
F_GETFL F_SETFL
F_GETOWN F_SETOWN
F_GETLK F_SETLK F_SETLKW
```

### File and Dir

```c
#include <sys/stat.h>
int stat(const char *restrict pathname, struct stat *restrict buf)
int fstat(int fd, struct stat *buf)
int lstat(consts char *restrict pathname, struct stat *restrict buf)
int fstatat(int fd, const char *restrict pathname, struct stat *restrict buf, int flag)
where 'flag' is AT_SYMLINK_NOFOLLOW
```

```c
#include <unistd.h>
int access(const char *pathkame, int mode)
int faccessat(int fd, const char *pathname, int mode, int flag)
where 'mode' is R_OK W_OK X_OK
```

```c
#include <unistd.h>
mode_t umask(mode_t cmask)
```

```c
#include <sys/stat.h>
int chmod(const char *pathname, mode_t mode)
int fchmod(int fd, mode_t mode)
int fchmodat(int fd, const char *pathname, mode_t mode, int flag)
```

```c
#include <sys/stat.h>
int chown(const char *pathname, uid_t owner, gid_t group)
int fchown(int fd, uid_t owner, gid_t group)
int fchownat(int fd, const char *pathname, uid_t owner, gid_t group, int flag)
int lchown(const char *pathname, uid_t owner, gid_t group)
```

```c
#include <unistd.h>
int truncate(const char *pathname, off_t length)
int ftruncate(int fd, off_t length)
```

```c
#include <unistd.h>
int link(const char *existingpath, const char *newpath)
int linkat(int fd, const char *existingpath, int nfd, const char *newpath)
int unlink(const char *pathname)
int unlinkat(int fd, const char *pathname, int flag)
where 'flag' is AT_REMOVEDIR
```

```c
#include <stdio.h>
int remove(const char *pathname)
```

```c
#include <stdio.h>
int rename(const char *oldname, const char *newname)
int renameat(int oldfd, const char *oldname, int newfd, const char *newname)
```

```c
#include <unistd.h>
int symlink(const char *actualpath, const char *sympath)
int symlinkat(const char *actualpath, int fd, const char *sympath)
```

```c
#include <unistd.h>
ssize_t readlink(const char *restrict pathname, char *restrict buf, size_t bufsize)
ssize_t readlinkat(int fd, const char *restrict pathname, char *restrict buf, size_t bufsize)
```

```c
#include <sys/stat.h>
int futimens(int fd, const strust timespec times[2])
int utimensat(int fd, const char *path, const strust timespec times[2], int flag)
```

```c
#include <sys/time.h>
int utimes(const char *pathname, const struct timeval times[2])
```

```c
#include <sys/stat>
int mkdir(const char *pathname, mode_t mode)
int mkdirat(int fd, const char *pathname, mode_t mode)
```

```c
#include <unistd.h>
int rmdir(const char *pathname)
```

```c
#include <dirent.h>
DIR *opendir(const char *pathname)
DIR *fdopendir(int fd)
struct dirent *readdir(DIR *dp)
void rewinddir(DIR *dp)
int closedir(DIR *dp)
long telldir(DIR *dp)
void seekdir(DIR *dp, long loc)
```

```c
#include <unistd.h>
int chdir(const char *pathname)
int fchdir(int fd)
char *getcwd(char *buf, size_t size)
```

### ISO/IO

```c
#include <stdio.h>
void setbuf(FILE *restrict fp, char *restrict buf)
void setvbuf(FILE *restrict fp, char *restrict buf, int mode, size_t size)
where 'mode' ir _IOFBF _IOLBF _IONBF

int fflush(FILE *fp)
```

```c
#include <stdio.h>
FILE *fopen(const char *restrict pathname, const char *restrict type)
FILE *freopen(const char *restrict pathname, const char *restrict type, FILE *restrict fd)
FILE *fdopen(int fd, const char *type)

int getc(FILE *fp)
int putc(int c, FILE *fp)
int fgetc(FILE *fp)
int fputc(int c, FILE *fp)
int getchar(void)
int putchar(int c)
int ferror(FILE *fp)
int feof(FILE *fp)
void clearerr(FILE *fp)
int ungetc(int c, FILE *fp)

char *fgets(char *restrict buf, int n, FILE *restrict fp)
char *gets(char *buf)
char *fputs(char *restrict str, FILE *restrict fp)
char *puts(const char *str)

size_t fread(void *restrict ptr, size_t size, size_t nobj, FILE *restrict fp)
size_t fwrite(const void *restrict ptr, size_t size, size_t nobj, FILE *restrict fp)

long ftell(FILE *fp)
int fseek(FILE *fp, long offset, int whence)
void rewind(FILE *fp)
off_t ftello(FILE *fp)
int fseeko(FILE *fp, off_t offset, int whence)
int fgetpos(FILE *restrict fp, fpos_t *restrict pos)
int fgetpos(FILE *fp, const fpos_t *pos)
```

```c
#include <stdio.h>
int printf(const char *restrict format, ...)
int fprintf(FILE *fp, const char *restrict format, ...)
int dprintf(int fd, const char *restrict format, ...)
int sprintf(char *restrict buf, const char *restrict format, ...)
int snprintf(char *restrict buf, size_t n, const char *restrict format, ...)

#include <stdarg.h>
#include <stdio.h>
int vprintf(const char *restrict format, va_list arg)
int vfprintf(FILE *fp, const char *restrict format, va_list arg)
int vdprintf(int fd, const char *restrict format, va_list arg)
int vsprintf(char *restrict buf, const char *restrict format, va_list arg)
int vsnprintf(char *restrict buf, size_t n, const char *restrict format, va_list arg)
```

```c
#include <stdio.h>
int scanf(const char *restrict format, ...)
int fscanf(FILE *restrict fp, const char *restrict format, ...)
int sscanf(const char *restrict buf, const char *restrict format, ...)

#include <stdarg.h>
#include <stdio.h>
int vscanf(const char *restrict format, va_list arg)
int vfscanf(FILE *restrict fp, const char *restrict format, va_list arg)
int vsscanf(const char *restrict buf, const char *restrict format, va_list arg)
```

```c
#include <stdio.h>
int fileno(FILE *fp)    //get 'fd'
char *tmpnam(char *ptr) //get temp file name
FILE *tmpfile(void)     //get temp file fp

#include <stdlib.h>
char *mkdtemp(char *template)   //get temp dir
int mkftemp(char *template)     //get temp file
where 'template' is a path like '/some/path/××××××'
```

```c
#include <stdio.h>
FILE *fmemopen(void *restrict buf, size_t size, const char *restrict type)
FILE *open_memstream(char **bufp, size_t *sizep)
```
### System Data File
```c
#include <pwd.h>
struct passwd *getpwuid(uid_t uid)
struct passwd *getpwnam(const char *name)
struct passwd *getpwent(void)   //get next 'passwd' entry
void setpwent(void)             //reset the passwd file
void endpwent(void)             //end 'getpwent'

#include <shadow.h>
struct spwd *getspnam(const char *name)
struct spwd *getspent(void)   //get next 'shadow' entry
void setspent(void)             //reset the shadow file
void endspent(void)             //end 'getspent'

#include <grp.h>
struct group *getgrgid(gid_t gid)
struct group *getgrnam(const char *name)
struct group *getgrent(void)   //get next 'group' entry
void setgrent(void)             //reset the group file
void endgrent(void)             //end 'getgrent'

#include <unistd.h>
int getgroups(int gidsetsize, gid_t grouplist[])

#include <grp.h>
#include <unistd.h>
int setgroups(int ngroups, const gid_t grouplist[])

#include <grp.h>
#include <unistd.h>
int initgroups(const char *username, git_t basegid)

//others
/etc/hosts      <netdb.h>   hostent getnameinfo     getaddrinfo
/etc/networks   <netdb.h>   netent  getnetbyname    getnetbyaddr
/etc/protocols  <netdb.h>   protent getporotobyname getprotobynumber
/etc/services   <netdb.h>   servent getservbyname   getservbyport
```

```c
#include <sys/usname.h>
int uname(struct usname *name)

#include <unistd.h>
int gethostname(char *name, int namelen)
```

```c
#include <time.h>
time_t time(time_t *calptr)
struct tm *gmtime(const time_t *calptr)
struct tm *localtime(const time_t *calptr)

time_t mktime(struct tm *tmptr)

size_t strftime(char *restrict buf, size_t maxsize,
                const char *restrict format,
                const struct tm *restrict tmptr)
size_t strftime_l(char *restrict buf, size_t maxsize,
                const char *restrict format,
                const struct tm *restrict tmptr, locale_t locale)
char *strptime(const char *restrict buf, const char *restrict format,
                const struct tm *restrict tmptr)

#include <sys/time.h>
int clock_gettime(clockid_t clock_id, struct timespec *tsp)
int clock_getres(clockid_t clock_id, struct timespec *tsp)
int clock_settime(clockid_t clock_id, struct timespec *tsp)
where 'clock_id' is CLOCK_REALTIME CLOCK_MONOTONIC CLOCK_PROCESS_CPUTIME_ID CLOCK_THREAD_CPUTIME_ID
```

### Process Environment

```c
#include <stdlib.h>
void exit(int status)
void Exit(int status)

#include <unistd.h>
void _exit(int status)

#include <stdlib.h>
void atexit(void (*func)(void))
```

```c
#include <stdlib.h>
void *malloc(size_t size)
void *calloc(size_t nobj, size_t size)
void *realloc(void *ptr, size_t newsize)
void free(void *ptr)
```

```c
#include <stdlib.h>
char *getenv(const char *name)
int putenv(char *str)
int setenv(const char*name, const char *value, int rewrite)
where 'rewrite' is 0 1
int unsetenv(const char *name)
```

```c
#include <setjmp.h>
int setjmp(jmp_buf env)
void longjmp(jmp_buf env, int val)
```

```c
#include <sys/resource.h>
int getrlimit(int resource, struct rlimit *rlptr)
int setrlimit(int resource, const struct rlimit *rlptr)
    struct rlimit {
        rlim_t  rlim_cur;
        rlim_t  rlim_max;
    }
```

### Process Controller

```c
#include <unistd.h>
pid_t getpid(void)
pid_t getppid(void)
uid_t getuid(void)
uid_t getepid(void)
gid_t getgid(void)
gid_t getegid(void)

int setuid(uid_t uid)
int setgid(gid_t gid)
int setreuid(uid_t ruid, uid_t euid)
int setregid(gid_t rgid, gid_t egid)
int seteuid(uid_t uid)
int setegid(gid_t gid)

pid_t fork(void)

#include <sys/wait.h>
pid_t wait(int *statloc)
pid_t waitpid(pid_t pid, int *statloc, int options)
int waitid(idtype_t idtype, id_t id, siginfo_t *infop, int options)

//others: wait3 wait4
```

```c
#include <stdlib.h>
int system(const char *cmdstring)
```
```c
#include <unistd.h>
char *getlogin(void)
int nice(int incr)

#include <sys/resource.h>
int getpriority(int which, id_t who)
int setpriority(int which, id_t who, int value)
where 'which' is PRIO_PROCESS PRIO_PGRP PRIO_USER
```

```c
#include <sys/times.h>
clock_t times(struct tms *buf)
```

### Process Relationship

```c
#include <unistd.h>
pid_t getpgid(pid_t pid)
pid_t getpgrp()
int setpgid(pid_t pid, pid_t pgid)

pid_t setsid(void)
pid_t getsid(pid_t pid)
pid_t tcgetpgrp(int fd)
int tcsetpgrp(int fd, pid_t pgrpid)

#include <termios.h>
pid_t tcgetsid(int fd)
```

### Signal Controller
