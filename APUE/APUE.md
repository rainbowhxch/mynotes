# APUE NOTE

## Preface
我认为这种类型的书籍还是应该把重心放在Coding上面，所以本文还是只提供API的笔记，如果读者有疑惑的话，建议直接查看书籍原文(原文写的很棒)，或通过搜索引擎查阅相关资料。

## Error Option
```c
// 根据错误代码 (errno) 返回错误信息
#include <string.h>
char *strerror(int errnum)
```

```c
// 同上，并附加提供的 msg
#include <stdio.h>
void perror(const char *msg)
```

## File Operation
```c
// 打开文件
#include <fcntl.h>
int open(const char *path, int Oflag, .../* mode_t mode */)
int openat(int fd, const char *path, int Oflag, .../* mode_t mode */)
当'fd'是AT_FDCWD时,path是相对路径,否则path是相对于打开目录(fd)的路径
where 'Oflag' is: O_RDYNLY O_WRONLY O_RDWR O_EXEC O_SEARCH O_APPEND
O_CLOEXEC O_CREAT O_NOCTTY O_NOFOLLOW O_NONBLOCK O_SYNC O_TRUNC O_TTY_INIT
O_DSYNC O_RSYNC
```

```c
// 创建文件，没特殊需要建议使用 open()
#includs <fcntl.h>
int creat(const char *path, mode_t mode)
```

```c
// 关闭文件
#include <unistd.h>
int close(int fd)
```

```c
// 设置当前文件偏移量
#include <unistd.h>
off_t lseek(int fd, off_t offset, int whence)
where 'whence' is SEEK_SET SEEK_CUR SEEK_END
```

```c
// 无缓冲读写文件
#include <unistd.h>
ssize_t read(int fd, void *buf, size_t nbytes)
ssize_t write(int fd, void *buf, size_t nbytes)
```

```c
// 无缓冲读写文件(原子操作)
#include <unistd.h>
ssize_t pread(int fd, void *buf, size_t nbytes, off_t offset)
ssize_t pwrite(int fd, void *buf, size_t nbytes, off_t offset)
```

```c
// 复制文件描述符
#include <unistd.h>
int dup(int fd)
int dup2(int fd, int fd2)
```

```c
// 将延迟写的数据同步到磁盘
#include <unistd.h>
// 下述俩函数保证已修改的块写回到磁盘再返回
int fsync(int fd)
int fdatasync(int fd)
// 这个函数只是将修改过的块缓存区排入写队列
void sync(void)
```

```c
// 改变文件属性
#include <fcntl.h>
int fcntl(int fd, int cmd, .../* int arg */)
where 'cmd' is
F_DUPFD F_DUPFD_CLOEXEC
F_GETFD F_SETFD
F_GETFL F_SETFL
F_GETOWN F_SETOWN
F_GETLK F_SETLK F_SETLKW
```

```c
// 各种 I/O 操作
#include <unistd.h>
#include <sys/ioctl.h>
int ioctl(int fd, int request, ...)
```

## File and Dir
```c
// 文件的相关属性
#include <sys/stat.h>
int stat(const char *restrict pathname, struct stat *restrict buf)
int fstat(int fd, struct stat *buf)
// 符号文件的相关属性
int lstat(consts char *restrict pathname, struct stat *restrict buf)
int fstatat(int fd, const char *restrict pathname, struct stat *restrict buf, int flag)
where 'flag' is AT_SYMLINK_NOFOLLOW

struct stat {
              dev_t     st_dev;         /* ID of device containing file */
              ino_t     st_ino;         /* Inode number */
              mode_t    st_mode;        /* File type and mode */
              nlink_t   st_nlink;       /* Number of hard links */
              uid_t     st_uid;         /* User ID of owner */
              gid_t     st_gid;         /* Group ID of owner */
              dev_t     st_rdev;        /* Device ID (if special file) */
              off_t     st_size;        /* Total size, in bytes */
              blksize_t st_blksize;     /* Block size for filesystem I/O */
              blkcnt_t  st_blocks;      /* Number of 512B blocks allocated */

              /* Since Linux 2.6, the kernel supports nanosecond
                 precision for the following timestamp fields.
                 For the details before Linux 2.6, see NOTES. */

              struct timespec st_atim;  /* Time of last access */
              struct timespec st_mtim;  /* Time of last modification */
              struct timespec st_ctim;  /* Time of last status change */

          #define st_atime st_atim.tv_sec      /* Backward compatibility */
          #define st_mtime st_mtim.tv_sec
          #define st_ctime st_ctim.tv_sec
          };
S_ISUID() // 设置用户ID位
S_ISGID() // 这是组ID位
```
![APUE-file-type-macro](http://www.rainbowch.net/resource/APUE-file-type-macro.png)
![APUE-file-type-macro-plus](http://www.rainbowch.net/resource/APUE-file-type-macro-plus.png)
![APUE-file-access-permission](http://www.rainbowch.net/resource/APUE-file-access-permission.png)

```c
// 文件权限测试(按实际用户ID和实际组ID测试)
#include <unistd.h>
int access(const char *pathkame, int mode)
int faccessat(int fd, const char *pathname, int mode, int flag)
where 'mode' is R_OK W_OK X_OK
当'flag'为AT_EACCESS时按有效用户ID和有效组ID测试
```

```c
// 创建文件模式屏蔽字,屏蔽字被设置，创建的文件的相应位一定被关闭
#include <unistd.h>
mode_t umask(mode_t cmask)
```

```c
// 更改现有文件的访问权限
#include <sys/stat.h>
int chmod(const char *pathname, mode_t mode)
int fchmod(int fd, mode_t mode)
int fchmodat(int fd, const char *pathname, mode_t mode, int flag)
当'flag'为AT_SYMLINK_NOFOLLOW时，'fchmodat'不会跟随符号链接
```

```c
// 更改文件的用户ID和组ID
#include <sys/stat.h>
int chown(const char *pathname, uid_t owner, gid_t group)
int fchown(int fd, uid_t owner, gid_t group)
int fchownat(int fd, const char *pathname, uid_t owner, gid_t group, int flag)
int lchown(const char *pathname, uid_t owner, gid_t group)
```

```c
// 截断文件
#include <unistd.h>
int truncate(const char *pathname, off_t length)
int ftruncate(int fd, off_t length)
```

```c
// 创建/删除一个指向已存在的i节点的目录项
#include <unistd.h>
int link(const char *existingpath, const char *newpath)
int linkat(int efd, const char *existingpath, int nfd, const char *newpath, int flag) // 'flag' 为AT_SYMLINK_FOLLOW时，新链接指向符号链接本身
int unlink(const char *pathname)
int unlinkat(int fd, const char *pathname, int flag) // where 'flag' can be 'AT_REMOVEDIR', 此时相当于rmdir操作
```

```c
// 解除对文件或目录的链接
#include <stdio.h>
int remove(const char *pathname)
```

```c
// 重命名文件或目录
#include <stdio.h>
int rename(const char *oldname, const char *newname)
int renameat(int oldfd, const char *oldname, int newfd, const char *newname)
```

![APUE-symlink-situation](http://www.rainbowch.net/resource/APUE-symlink-situation.png)
```c
// 创建符号链接
#include <unistd.h>
int symlink(const char *actualpath, const char *sympath)
int symlinkat(const char *actualpath, int fd, const char *sympath)
```

```c
// 读取符号链接中的文件或目录名
#include <unistd.h>
ssize_t readlink(const char *restrict pathname, char *restrict buf, size_t bufsize)
ssize_t readlinkat(int fd, const char *restrict pathname, char *restrict buf, size_t bufsize)
```

```c
// 修改文件的访问和修改时间
#include <sys/stat.h>
int futimens(int fd, const strust timespec times[2])
int utimensat(int fd, const char *path, const strust timespec times[2], int flag)
```
![APUE-file-time](http://www.rainbowch.net/resource/APUE-file-time.png)

```c
// 修改文件的访问和修改时间
#include <sys/time.h>
int utimes(const char *pathname, const struct timeval times[2])

struct timeval {
    time_t tv_sec; /* seconds */
    long tv_usec; /* microseconds */
}
```

```c
// 创建空目录
#include <sys/stat>
int mkdir(const char *pathname, mode_t mode)
int mkdirat(int fd, const char *pathname, mode_t mode)
```

```c
// 删除空目录
#include <unistd.h>
int rmdir(const char *pathname)
```

```c
// 读目录
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
// 更改当前工作目录
#include <unistd.h>
int chdir(const char *pathname)
int fchdir(int fd)
// 获取当前工作目录
char *getcwd(char *buf, size_t size)
```
![APUE-file-access-permission-summry.png](http://www.rainbowch.net/resource/APUE-file-access-permission-summry.png)

## ISO/IO (标准I/O库)
```c
// 设置流的定向(只作用于未定向的流)
#include <stdio.h>
#include <wchar.h>
int fwide(FILE *fp, int mode)
'mode' 为负，试图使指定的流设置为单定向的
'mode' 为正，试图使指定的流设置为宽定向的
'mode' 为0，不设置流定向，但返回流定向的值
```

![APUE-buffer-type](http://www.rainbowch.net/resource/APUE-buffer-type.png)
![APUE-buffer-type2](http://www.rainbowch.net/resource/APUE-buffer-type2.png)
```c
// 更改流的缓冲类型
#include <stdio.h>
void setbuf(FILE *restrict fp, char *restrict buf) // 为了带缓冲进行I/O，参数buf必须指向一个长度为BUFSIZ(定义在<stdio.h>中)的缓冲区
void setvbuf(FILE *restrict fp, char *restrict buf, int mode, size_t size)
where 'mode' is _IOFBF(全缓冲) _IOLBF(行缓冲) _IONBF(不带缓冲)

// 冲洗一个流，即将缓冲区的内容写到磁盘上
int fflush(FILE *fp)
```
![APUE-setbuf-setvbuf](http://www.rainbowch.net/resource/APUE-setbuf-setvbuf.png)

```c
#include <stdio.h>
// 打开一个标准I/O流
FILE *fopen(const char *restrict pathname, const char *restrict type)
// 在一个指定的流上打开指定的文件
FILE *freopen(const char *restrict pathname, const char *restrict type, FILE *restrict fd)
// 获得与文件描述符相结合的标准I/O流
FILE *fdopen(int fd, const char *type)
// 关闭流
int fclose(FILE *fp)
```
![APUE-fopen-type](http://www.rainbowch.net/resource/APUE-fopen-type.png)

```c
// 读写一个字符
int getc(FILE *fp)
int putc(int c, FILE *fp)
int fgetc(FILE *fp)
int fputc(int c, FILE *fp)
int getchar(void)
int putchar(int c)
// 判断I/O是否出错
int ferror(FILE *fp)
// 判断I/O是否到达EOF
int feof(FILE *fp)
// 清除文件出错标志和文件结束标志
void clearerr(FILE *fp)
// 将字符再压送回流中
int ungetc(int c, FILE *fp)
```

```c
// 读写一行
char *fgets(char *restrict buf, int n, FILE *restrict fp)
char *gets(char *buf)
char *fputs(char *restrict str, FILE *restrict fp)
char *puts(const char *str)
```

```c
// 二进制I/O
size_t fread(void *restrict ptr, size_t size, size_t nobj, FILE *restrict fp)
size_t fwrite(const void *restrict ptr, size_t size, size_t nobj, FILE *restrict fp)

// 获取文件偏移量
long ftell(FILE *fp)
// 设置文件偏移量
int fseek(FILE *fp, long offset, int whence)
// 设置文件偏移量为0
void rewind(FILE *fp)
// 同上，但依靠off_t 数据类型实现
off_t ftello(FILE *fp)
int fseeko(FILE *fp, off_t offset, int whence)
int fgetpos(FILE *restrict fp, fpos_t *restrict pos)
int fgetpos(FILE *fp, const fpos_t *pos)
```

```c
// 格式化输出
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
// 格式化输入
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
// 获得与标准I/O流相关联的文件描述符
#include <stdio.h>
int fileno(FILE *fp)    // get 'fd'
// 创建临时文件名
char *tmpnam(char *ptr) // ptr: temp file name, ptr指向的数组长度至少应该为L_timpnam(定义在<stdio.h>中)
// 创建临时文件
FILE *tmpfile(void)     // get temp file fp

#include <stdlib.h>
// 创建临时目录
char *mkdtemp(char *template)   // 返回指向目录名的指针
// 创建临时文件
int mkftemp(char *template)     // 返回文件描述符
where 'template' is a path like '/some/path/××××××'
```

```c
// 创建内存流
#include <stdio.h>
FILE *fmemopen(void *restrict buf, size_t size, const char *restrict type)
FILE *open_memstream(char **bufp, size_t *sizep) // 面向字节
#include <wchar.h>
FILE *open_wmemstream(char **bufp, size_t *sizep) // 面向宽字节
```

## System Data File
![APUE-passwd-struct](http://www.rainbowch.net/resource/APUE-passwd-struct.png)
```c
#include <pwd.h>
// 获取指定口令文件项
struct passwd *getpwuid(uid_t uid)
struct passwd *getpwnam(const char *name)
// 迭代获取所有口令文件项
struct passwd *getpwent(void)   //get next 'passwd' entry
// 反饶读取的口令文件
void setpwent(void)             //reset the passwd file
// 关闭读取的口令文件
void endpwent(void)             //end 'getpwent'
```

![APUE-shadow-struct](http://www.rainbowch.net/resource/APUE-shadow-struct.png)
```c
#include <shadow.h>
struct spwd *getspnam(const char *name)
struct spwd *getspent(void)   //get next 'shadow' entry
void setspent(void)             //reset the shadow file
void endspent(void)             //end 'getspent'
```

![APUE-group-struct](http://www.rainbowch.net/resource/APUE-group-struct.png)
```c
#include <grp.h>
struct group *getgrgid(gid_t gid)
struct group *getgrnam(const char *name)
struct group *getgrent(void)   //get next 'group' entry
void setgrent(void)             //reset the group file
void endgrent(void)             //end 'getgrent'

#include <unistd.h>
// 获取附属组ID
int getgroups(int gidsetsize, gid_t grouplist[]) // 最多填写gidsetsize个，返回实际填写个数

#include <grp.h>
#include <unistd.h>
// 设置附属组ID
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
![APUE-system-data-summry](http://www.rainbowch.net/resource/APUE-system-data-summry.png)

```c
#include <sys/usname.h>
// 获取与主机和操作系统相关的信息
int uname(struct usname *name)
struct utsname {
    char sysname[];    /* Operating system name (e.g., "Linux") */
    char nodename[];   /* Name within "some implementation-defined
                         network" */
    char release[];    /* Operating system release (e.g., "2.6.28") */
    char version[];    /* Operating system version */
    char machine[];    /* Hardware identifier */
    #ifdef _GNU_SOURCE
    char domainname[]; /* NIS or YP domain name */
    #endif
};

// 获取主机名
#include <unistd.h>
int gethostname(char *name, int namelen)
```

```c
#include <time.h>
// 返回UTC时间
time_t time(time_t *calptr)
// 将日历时间转换成分解的时间
struct tm *gmtime(const time_t *calptr)
struct tm *localtime(const time_t *calptr)
struct tm {
    int tm_sec;    /* Seconds (0-60) */
    int tm_min;    /* Minutes (0-59) */
    int tm_hour;   /* Hours (0-23) */
    int tm_mday;   /* Day of the month (1-31) */
    int tm_mon;    /* Month (0-11) */
    int tm_year;   /* Year - 1900 */
    int tm_wday;   /* Day of the week (0-6, Sunday = 0) */
    int tm_yday;   /* Day in the year (0-365, 1 Jan = 0) */
    int tm_isdst;  /* Daylight saving time */
};

// 将tm转换成time_t
time_t mktime(struct tm *tmptr)

// 格式化输出时间至相应字符串
size_t strftime(char *restrict buf, size_t maxsize,
                const char *restrict format,
                const struct tm *restrict tmptr)
size_t strftime_l(char *restrict buf, size_t maxsize,
                const char *restrict format,
                const struct tm *restrict tmptr, locale_t locale)
char *strptime(const char *restrict buf, const char *restrict format,
                const struct tm *restrict tmptr)

#include <sys/time.h>
// 返回相应时钟类型的精确时间
int clock_gettime(clockid_t clock_id, struct timespec *tsp)
// 返回相应时钟类型的精度
int clock_getres(clockid_t clock_id, struct timespec *tsp)
// 设置相应时钟类型的精确时间
int clock_settime(clockid_t clock_id, struct timespec *tsp)
where 'clock_id' is CLOCK_REALTIME CLOCK_MONOTONIC CLOCK_PROCESS_CPUTIME_ID CLOCK_THREAD_CPUTIME_ID
```
![APUE-time-type](http://www.rainbowch.net/resource/APUE-time-type.png)

## Process Environment
```c
#include <stdlib.h>
// 执行清理操作后终止进程
void exit(int status)

// 直接终止进程
void _Exit(int status)
// 直接终止进程
#include <unistd.h>
void _exit(int status)

// 设置终止处理程序，进程终止时自动被调用(按后入先出顺序)
#include <stdlib.h>
void atexit(void (*func)(void))
```

```c
#include <stdlib.h>
// 分配指定字节的存储空间(堆上)
void *malloc(size_t size)
// 分配指定字节的存储空间(堆上), 并初始为0
void *calloc(size_t nobj, size_t size)
// 减少或增加以前存储空间的长度
void *realloc(void *ptr, size_t newsize)
// 释放存储空间
void free(void *ptr)
```

```c
#include <stdlib.h>
// 获取相应的环境变量项
char *getenv(const char *name)
// 设置相应的环境变量项, 若已存在，删除重设
int putenv(char *str)
// 设置相应的环境变量项, rewrite控制若环境变量已存在时的行为(0:不删除直接返回，1:删除重设为value)
int setenv(const char*name, const char *value, int rewrite)
// 删除相应的环境变量项
int unsetenv(const char *name)
```

```c
// 非局部跳转：可跨越多个栈帧的跳转
#include <setjmp.h>
// 设置跳转位置
int setjmp(jmp_buf env)
// 开始跳转
void longjmp(jmp_buf env, int val)
```

```c
#include <sys/resource.h>
// 获取进程资源限制参数
int getrlimit(int resource, struct rlimit *rlptr)
// 设置进程资源限制参数
int setrlimit(int resource, const struct rlimit *rlptr)
struct rlimit {
    rlim_t  rlim_cur;
    rlim_t  rlim_max;
}
```

## Process Controller
```c
// 获取进程标识符
#include <unistd.h>
pid_t getpid(void)
pid_t getppid(void)
uid_t getuid(void)
uid_t getepid(void)
gid_t getgid(void)
gid_t getegid(void)

// 设置进程标识符
int setuid(uid_t uid)
int setgid(gid_t gid)
int setreuid(uid_t ruid, uid_t euid)
int setregid(gid_t rgid, gid_t egid)
int seteuid(uid_t uid)
int setegid(gid_t gid)

// 创建新的子进程
pid_t fork(void) // 返回两次，在父进程中返回子进程ID, 在子进程中返回0

// 父进程等待子进程返回
#include <sys/wait.h>
pid_t wait(int *statloc) // 返回子进程ID, statloc 是子进程的终止状态, 可用下述宏获得一些终止相关信息
pid_t waitpid(pid_t pid, int *statloc, int options)
int waitid(idtype_t idtype, id_t id, siginfo_t *infop, int options)
//others: wait3 wait4

// 当前进程执行指定程序 (函数名带p的同时使用PATH环境变量查找文件，l表示参数表，v表示argv[]矢量，e表示提供envp[]数组)
#include <unistd.h>
int execl(const char *pathname, const char *arg0, ... /* (char *)0 */ ); // 命令行参数需要以空指针结尾, 继承调用进程中的环境变量
int execv(const char *pathname, char *const argv[]);
int execle(const char *pathname, const char *arg0, ... /* (char *)0, char *const envp[] */ );
int execve(const char *pathname, char *const argv[], char *const envp[]);
int execlp(const char *filename, const char *arg0, ... /* (char *)0 */ ); // 未找到指定的可执行文件后，则认为该文件为shell脚本
int execvp(const char *filename, char *const argv[]) // 未找到指定的可执行文件后，则认为该文件为shell脚本
int fexecve(int fd, char *const argv[], char *const envp[]);
```
![APUE-wait-statloc-macro](http://www.rainbowch.net/resource/APUE-wait-statloc-macro.png)

```c
// 执行命令行命令
#include <stdlib.h>
int system(const char *cmdstring)
```

```c
#include <unistd.h>
// 获得运行该程序的登录名
char *getlogin(void)
```

```c
// 更改进程调度的优先级，incr为增加的值(即nice += incr), nice值越高优先级越低，反之亦然
#include <unistd.h>
int nice(int incr) // 'NZERO'时系统默认的nice值

// 获得和设置nice值
#include <sys/resource.h>
int getpriority(int which, id_t who)
int setpriority(int which, id_t who, int value)
where 'which' is PRIO_PROCESS PRIO_PGRP PRIO_USER
```

```c
// 获取墙上时钟时间、用户CPU时间和系统CPU时间
#include <sys/times.h>
clock_t times(struct tms *buf) // 返回墙上时钟时间
struct tms {
    clock_t tms_utime; /* user CPU time */
    clock_t tms_stime; /* system CPU time */
    clock_t tms_cutime; /* user CPU time, terminated children */
    clock_t tms_cstime; /* system CPU time, terminated children */
}
```

## Process Relationship
```c
// 获取和设置进程组ID
#include <unistd.h>
pid_t getpgid(pid_t pid)
pid_t getpgrp()
// 为本身或子进程设置进程组ID
int setpgid(pid_t pid, pid_t pgid)

// 创建新会话
pid_t setsid(void) // 该进程变为新会话的会话首进程
// 获取会话首进程ID
pid_t getsid(pid_t pid)
// 获取前台进程组ID
pid_t tcgetpgrp(int fd) // 'fd'为TTY的文件描述符
// 设置前台进程组ID
int tcsetpgrp(int fd, pid_t pgrpid)

// 获取会话首进程ID
#include <termios.h>
pid_t tcgetsid(int fd)
```
![APUE-session-and-control-terminal](http://www.rainbowch.net/resource/APUE-session-and-control-terminal.png)

## Signal Controller
```c
#include <signal.h>
void (*signal(int signo, void (*func)(int)))(int)
where 'signo' is signal, 'func' is SIG_IGN SIG_DFL or a function pointor
int kill(pid_t pid, int signo)
int raise(int signo)

#include <unistd.h>
unsigned int alarm(unsigned int seconds)
int pause(void)

#include <signal.h>
int sigemptyset(sigset_t *set)
int sigfillset(sigset_t *set)
int sigaddset(sigset_t *set, int signo)
int sigdelset(sigset_t *set, int signo)
int sigismember(const sitset_t *set, int signo)
int sigprocmask(int how, const sigset_t *restrict set, sigset_t *restrict oset)
where 'how' is SIG_BLOCK SIG_UNBLOCK SIG_SETMASK
int sigaction(int signo, const struct sigaction *restrict act, struct sigaction *restrict oact)
struct sigaction {
    void (*sa_handler)(int);
    sigset_t sa_mask;
    int sa_flags;
    void (*sa_sigaction)(int signo, siginfo_t *info, void *context)
}
```
