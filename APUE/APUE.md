ze# APUE NOTE

## Preface
我认为这种类型的书籍还是应该把重心放在Coding上面，所以本文还是只提供API的笔记，千万不要通过变量名猜测变量意图，如果读者有疑惑的话，建议直接查看书籍原文(原文写的很棒)，或通过搜索引擎查阅相关资料。

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
pid_t setsid(void) // 该进程变为新会话的会话首进程,并成为一个新进程组的组长，再之切断其控制终端
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
![APUE-signal-type](http://www.rainbowch.net/resource/APUE-signal-type.png)
![APUE-signal-handlable-functions](http://www.rainbowch.net/resource/APUE-signal-handlable-functions.png)
```c
#include <signal.h>
// 信号捕捉函数
void (*signal(int signo, void (*func)(int)))(int) // 'signo' 为上述信号之一，'func' 为'SIG_IGN'、'SIG_DFL'或函数地址, 返回上一个设置的处理函数地址
// 将信号发送给进程或进程组
int kill(pid_t pid, int signo)
// 将信号发送给自己
int raise(int signo)

#include <unistd.h>
// 设置定时器，到时产生SIGALRM信号
unsigned int alarm(unsigned int seconds) // 返回剩余时间
// 挂起进程，直至捕捉一个信号
int pause(void)

#include <signal.h>
// 处理信号集
int sigemptyset(sigset_t *set)
int sigfillset(sigset_t *set)
int sigaddset(sigset_t *set, int signo)
int sigdelset(sigset_t *set, int signo)
int sigismember(const sitset_t *set, int signo)
// 检测或更改信号屏蔽字
int sigprocmask(int how, const sigset_t *restrict set, sigset_t *restrict oset)
where 'how' is SIG_BLOCK SIG_UNBLOCK SIG_SETMASK
// 返回调用进程阻塞的信号集
int sigpending(sigset_t *set)
```
![APUE-sigprocmask-how](http://www.rainbowch.net/resource/APUE-sigprocmask-how.png)

```c
// 检测或更改信号相关联的处理动作
int sigaction(int signo, const struct sigaction *restrict act, struct sigaction *restrict oact)
struct sigaction {
    void (*sa_handler)(int);
    sigset_t sa_mask;
    int sa_flags;
    void (*sa_sigaction)(int signo, siginfo_t *info, void *context)
}
```
![APUE-sigaction-sa_flags](http://www.rainbowch.net/resource/APUE-sigaction-sa_flags.png)

```c
// 保存信号屏蔽字的非本地跳转
#include <setjmp.h>
int sigsetjmp(sigsetjmp_buf env, int savemask)
void siglongjmp(sigsetjmp_buf env, int val)
```

```c
// 设置信号屏蔽字并挂起进程，直至捕捉到一个信号，然后恢复调用本函数之前的信号屏蔽字并返回
#include <signal.h>
int sigsuspend(const sigset_t *sigmask)
```

```c
/ 使程序异常终止
#include <stdlib.h>
void abort(void)
```

```c
#include <unistd.h>
// 进程挂起'seconds'秒，或进程捕捉到一个信号
unsigned int sleep(unsigned int seconds)
#include <time.h>
// 提供纳秒级精度，若因为信号被中断，则剩余时间保存在'remtp'中
int nanosleep(const struct timespec *reqtp, struct timespec *remtp)
// 提供特定时钟
int clock_nanosleep(clockid_t clock_id, int flags,
                    const struct timespec *reqtp, struct timespec *remtp) /* 'flags'为0，表示相对时间，为'TIMER_ABSTIME'表示绝对时间 */
```

```c
#include <signal.h>
// 发送排队信号
int sigqueue(pid_t pid, int signo, const union sigval value) /* 'value'是信号额外的信息, 可以用'sigaction'函数的'SA_SIGINFO'标志获得额外信息 */
// 打印与信号编号对应的字符串
void psignal(int signo, const char *msg) /* 类似perror */
void psiginfo(const siginfo_t *info, const char *msg)
// 获取与信号编号对应的字符串
char *strsignal(int signo)
// Solaris提供函数在信号编号与信号名之间转换
int sig2str(int signo, char *str)
int str2sig(const char *str, int *signop)
```
![APUE-job-control](http://www.rainbowch.net/resource/APUE-job-control.png)

## Thread
```c
#include <pthread.h>
// 比较两个线程ID
int pthread_equal(pthread_t tid1, pthread_t tid2)
// 返回本线程ID
pthread_t pthread_self(void)
// 创建新线程
int pthread_create(pthread_t *restrict tidp, const pthread_attr_t *restrict attr,
                   void *(*start_rtn)(void *), void *restrict arg) /* 'tidp'被填充为新创建的线程ID, 'start_rtn'是线程运行的地址，'arg'是参数 */
// 终止线程
void pthread_exit(void *rval_ptr) /* ‘rval_ptr'被'pthread_join'使用 */
// 阻塞等待指定线程退出
int pthread_join(pthread_t thread, void **rval_ptr) /* 如果指定线程被取消而不是从启动例程中返回则'rval_ptr'被设置成'PTHREAD_CANCELED' */
// 请求取消同一进程中的其他线程
int pthread_cancel(pthread_t tid)
// 设置线程清理处理程序
void pthread_cleanup_push(void (*rtn)(void *), void *arg) /* 类似于'atexit()' */
// 执行或取消线程清理处理程序
void pthread_cleanup_pop(int execute) /* 'execute'非0时执行弹出的线程清理处理程序并执行，为0时，不执行 */
// 默认情况下，线程的终止状态会保存到被调用pthread_join，但如果线程被分离底层存储资源则立即收回，分离后不能对该线程调用pthread_join
int pthread_detach(pthread_t tid)
```

### 互斥量
```c
#include <pthread.h>
// 初始化互斥量
int pthread_mutex_init(pthread_mutex_t *restrict mutex, const pthread_muutexattr_t *restrict attr) /* 用自定义的属性初始化互斥量需指定'attr', 否则为NULL为默认属性 */
// 销毁动态分配的互斥量
int pthread_mutex_destroy(pthread_mutex_t *mutex)
// 对互斥量上锁，若已上锁，则阻塞
int pthread_mutex_lock(pthread_mutex_t *mutex)
// 对互斥量上锁，不阻塞，若已上锁，则返回'EBUSY'
int pthread_mutex_trylock(pthread_mutex_t *mutex)
// 对互斥量解锁
int pthread_mutex_unock(pthread_mutex_t *mutex)
#include <pthread.h>
#include <time.h>
// 同'pthread_mutex_lock'，但有时间限制，若超过时间返回'ETMEDOUT'，不再阻塞
int pthread_mutex_timedlock(pthread_mutex_t *restrict mutex, const struct timespec *restrict tsptr)
```

### 读写锁
```c
#include <pthread.h>
// 初始化读写锁
int pthread_rwlock_init(pthread_rwlock_t *restrict rwlock, const pthread_rwlockattr_t *restrict attr)
// 销毁动态分配的读写锁
int pthread_rwlock_destory(pthread_rwlock_t *rwlock)
// 读模式加锁, 以读模式加锁的线程可以得到访问权，但以写模式加锁的线程会被阻塞
int pthread_rwlock_rdlock(pthread_rwlock_t *rwlock)
// 写模式加锁, 所有尝试加锁的其他线程会被阻塞
int pthread_rwlock_wrlock(pthread_rwlock_t *rwlock)
// 解锁
int pthread_rwlock_unlock(pthread_rwlock_t *rwlock)
// 尝试以读模式加锁，失败则直接返回错误'EBUSY'
int pthread_rwlock_tryrdlock(pthread_rwlock_t *rwlock)
// 尝试以写模式加锁，失败则直接返回错误'EBUSY'
int pthread_rwlock_trywrlock(pthread_rwlock_t *rwlock)
// 带有超时的读写锁
int pthread_rwlock_timedrdlock(pthread_rwlock_t *restrict rwlock, const struct timespec *restrict tsptr)
int pthread_rwlock_timedwrlock(pthread_rwlock_t *restrict rwlock, const struct timespec *restrict tsptr)
```

### 条件变量
```c
#include <pthread.h>
// 初始化条件变量
int pthread_cond_init(pthread_cond_t *restrict cond, const pthread_condattr_t *restrict attr)
// 销毁条件变量
int pthread_cond_destroy(pthread_cond_t *cond)
// 等待条件成立
int pthread_cond_wait(pthread_cond_t *restrict cond, pthread_mutex_t *restrict mutex)
// 带有超时等待条件成立
int pthread_cond_timedwait(pthread_cond_t *restrict cond, pthread_mutex_t *restrict mutex, const struct timespec *restrict tsptr)
// 通知一个线程条件已满足
int pthread_cond_signal(pthread_cond_t *cond)
// 通知所有线程条件已满足
int pthread_cond_broadcast(pthread_cond_t *cond)
```

### 自旋锁
```c
/* 自旋锁与互斥量类似但它不是通过休眠使进程阻塞，而是在获取锁之前一直忙等 */
#include <pthread.h>
int pthread_spin_init(pthread_spinlock_t *lock, int pshared) /* 'pshared'为'PTHREAD_PROCESS_SHARED'便是可被能访问锁底层内存的所有线程获取，'PTHREAD_PROCESS_PRIVATE'表示智能被初始化该锁的进程内部的线程访问 */
int pthread_spin_destroy(pthread_spinlock_t *lock)
int pthread_spin_lock(pthread_spinlock_t *lock)
int pthread_spin_trylock(pthread_spinlock_t *lock)
int pthread_spin_unlock(pthread_spinlock_t *lock)
```

### 屏障
```c
/* 屏障可以保证一定数量的线程达到某种状态时再继续执行 */
#include <pthread.h>
int pthread_barrier_init(pthread_barrier_t *restrict barrier, const pthread_barrierattr_t *restrict attr,
                        unsigned int count) */ 'count'说明需要多少线程完成工作 */
int pthread_barrier_destroy(pthread_barrier_t *barrier)
// 说明本线程已完成工作，等待其他线程完成
int pthread_barrier_wait(pthread_barrier_t *barrier)
```

## 线程控制
### 线程属性
```c
#include <pthread.h>
int pthread_attr_init(pthread_attr_t *attr)
int pthread_attr_destroy(pthread_attr_t *attr)
// 设置线程的分离状态
int pthread_attr_getdetachstate(const pthread_attr_t *restrict attr, int *detachstate) /* 'detachstate'为'PTHREAD_CREATE_DETACHED'或'PTHREAD_CREATE_JOINABLE' */
int pthread_attr_setdetachstate(const pthread_attr_t *attr, int *detachstate)
// 设置线程的栈属性
int pthread_attr_getstack(const pthread_attr_t *restrict attr, void **restrict stackaddr, size_t *restrict stacksize)
int pthread_attr_setstack(pthread_attr_t *attr, void *stackaddr, size_t stacksize)
int pthread_attr_getstacksize(const pthread_attr_t *restrict attr, size_t *restrict stacksize)
int pthread_attr_setstacksize(pthread_attr_t *attr, size_t stacksize) /* 'stacksize'不能小于'PTHREAD_STACK_MIN' */
// 设置线程栈末尾之后用以避免栈溢出的扩展内存的大小
int pthread_attr_getguardsize(const pthread_attr_t *restrict attr, size_t *restrict guardsize)
int pthread_attr_setguardsize(pthread_attr_t *attr, size_t guardsize)
```
### 同步属性
#### 互斥量属性
```c
int pthread_mutexattr_init(pthread_mutexattr_t *attr)
int pthread_mutexattr_destroy(pthread_mutexattr_t *attr)
// 进程共享属性
int pthread_mutexattr_getpshared(pthread_mutexattr_t *restrict attr, int *restrict pshared) /* 'pshared'为'PTHREAD_PROCESS_PRIVATE'时，互斥量可被进程中多个线程共享 */
int pthread_mutexattr_setpshared(pthread_mutexattr_t *attr, int pshared)
// 健壮属性
int pthread_mutexattr_getrobust(pthread_mutexattr_t *restrict attr, int *restrict robust) /* 'robust'为'PTHREAD_MUTEX_STALLED'时持有互斥量的进程终止时不采取特别动作，'PTHREAD_MUTEX_ROBUST'使其他等待锁的'pthread_mutex_lock'调用返回'EOWNERDEAD' */
int pthread_mutexattr_setrobust(pthread_mutexattr_t *attr, int robust)
// 指明与该互斥量相关的状态在互斥量解锁前时一致的
int pthread_mutexattr_consistent(pthread_mutex_t *mutex)
int pthread_mutexattr_getype(pthread_mutexattr_t *restrict attr, int *restrict type)
int pthread_mutexattr_settype(pthread_mutexattr_t *attr, int type)
```
![APUE-mutex-type](http://www.rainbowch.net/resource/APUE-mutex-type.png)

#### 读写锁属性
```c
#include <pthread.h>
int pthread_rwlockattr_init(pthread_rwlockattr_t *attr)
int pthread_rwlockattr_destroy(pthread_rwlockattr_t *attr)
int pthread_rwlockattr_getpshared(pthread_rwlockattr_t *restrict attr, int *restrict pshared)
int pthread_rwlockattr_setpshared(pthread_rwlockattr_t *attr, int pshared)
```

#### 条件变量属性
```c
#include <pthread.h>
int pthread_condattr_init(pthread_condattr_t *attr)
int pthread_condattr_destroy(pthread_condattr_t *attr)
int pthread_condattr_getpshared(pthread_condattr_t *restrict attr, int *restrict pshared)
int pthread_condattr_setpshared(pthread_condattr_t *attr, int pshared)
// 设置'pthread_cond_timedwait'采用哪个时钟
int pthread_condattr_getclock(pthread_condattr_t *restrict attr, clockid_t *restrict clock_id)
int pthread_condattr_setclock(pthread_condattr_t *attr, clockid_t clock_id)
```

#### 屏障属性
```c
#include <pthread.h>
int pthread_barrierattr_init(pthread_barrierattr_t *attr)
int pthread_barrierattr_destroy(pthread_barrierattr_t *attr)
int pthread_barrierattr_getpshared(pthread_barrierattr_t *restrict attr, int *restrict pshared)
int pthread_barrierattr_setpshared(pthread_barrierattr_t *attr, int pshared)
```
![APUE-thread-safed-functions](http://www.rainbowch.net/resource/APUE-thread-safed-functions.png)

### 标准I/O线程安全
```c
#include <stdio.h>
// FILE对象加解锁
int ftrylockfile(FILE *fp)
void flockfile(FILE *fp)
void funlockfile(FILE *fp)
// 不加锁版本的基于字符的标准I/O (建议在flockfile/funlockfile之间使用)
int getchar_unlocked(void)
int getc_unlocked(FILE *fp)
int putchar_unlocked(int c)
int putc_unlocked(intc, FILE *fp)
```

### 线程特定数据
```c
#include <pthread.h>
// 创建与线程特定数据相关联的一个键
int pthread_key_create(pthread_key_t *keyp, void (*destructor)(void *)) /* 'destructor'为析构函数，当与此键关联的数据不为空时且线程终止时被调用 */
// 删除键
int pthread_key_delete(pthread_key_t key) /* 析构函数不会被调用 */
// 在多个线程中保证函数被调用一次
pthread_once_t initflag = PTHREAD_ONCE_INIT;
int pthread_once(pthread_once_t *initflag, vod (*initfn)(void))
// 关联线程的键与特定数据
void *pthread_getspecific(pthread_key_t key)
int pthread_setspecific(pthread_key_t key, const void *value)
```

### 线程的取消选项
```c
#include <pthread.h>
// 设置可取消状态属性
int pthread_setcancelstate(int state, int *oldstate) /* 'state'为'PTHREAD_CANCEL_ENABLE'或'PTHREAD_CANCEL_DISABLE'，为'PTHREAD_CANCEL_DISABLE'时取消请求被阻塞直至'PTHREAD_CANCEL_ENABLE'被设置后再被取消 */
// 添加取消点
void pthread_testcancel(void)
int pthread_setcanceltype(int type, int *oldtype) /* 'type'为'PTHREAD_CANCEL_DEFERRED'或'PTHREAD_CANCEL_ASYNCHRONOUS' */
```

### 线程与信号
```c
#include <signal.h>
// 线程环境下的'sigprocmask'
int pthread_sigmask(int how, const sigset_t *restrict set, sigset_t *restrict oset)
// 等待一个或多个信号出现
int sigwait(const sigset_t *restrict set, int *restrict signop) /* 'set'是线程等待的信号集,'signop'将填充为等到的信号 */
// 向线程发送信号
int pthread_kill(pthread_t thread, int signo)
```

### 线程和Fork
```c
#include <pthread.h>
// 建立fork处理程序
int pthread_atfork(void (*prepare)(void), void (*parent)(void), void (*child)(void))
```

## 守护进程(daemon)
### 日志
```c
#include <syslog.h>
// 初始化日志消息选项
void openlog(const char *ident, int option, int facility) /* 可选，若不调用，'syslog'第一次调用时将自动调用 */
// 产生一条日志消息
void syslog(int priority, const char *format, ...) /* 'priority'由'facility'与'level'相或组成，'format'中'%m'会被替换成'errno'对应的出错消息 */
// 关闭用于与syslogd daemon进行通信的描述符
void closelog(void)
// 设置进程的记录优先级屏蔽字
int setlogmask(int maskpri)
```
![APUE-openlog-option](http://www.rainbowch.net/resource/APUE-openlog-option.png)
![APUE-openlog-facility](http://www.rainbowch.net/resource/APUE-openlog-facility.png)
![APUE-syslog-level](http://www.rainbowch.net/resource/APUE-syslog-level.png)

### 单例守护进程
考虑使用文件和记录锁实现
### 编程规则
1. 设置文件模式屏蔽字(调用umask())为已知值(通常为0)
2. 调用fork()，然后使父进程exit，保证父进程终止，保证子进程不是组长进程
3. 调用setsid创建一个新会话，(a)成为会话首进程(b)成为新进程组的组长进程(c)没有控制终端
4. 更改当前工作目录为根目录或者指定目录(chdir())
5. 关闭不再需要的文件描述符(close())
6. 使文件描述符0、1、2与/dev/null关联
### 守护进程惯例
- 若使用锁文件，应存储再/var/run/目录中，应name.pid命名
- 配置文件应放在/etc/目录下，应name.conf命名
- 通常由系统初始化脚本启动，如/etc/init.d/*下的脚本，若需要在终止时自动重启则需要再/etc/inittab中为该daemon包括respawn记录项
- 守护进程应在启动时读取一次配置文件，此后一般不再查看它，为使已修改的配置文件生效，应使守护进程捕捉SIGHUP信号，接受到信号后重新读取配置文件

## 高级I/O
### 记录锁
```c
#include <fcnl.h>
// 文件记录锁操作
int fcntl(int fd, int cmd, .../* struct flock *flockptr */)
struct flock {
    short l_type;    /* Type of lock: F_RDLCK(共享读锁), F_WRLCK(独占性写锁), F_UNLCK(解锁) */
    short l_whence;  /* How to interpret l_start:
                       SEEK_SET, SEEK_CUR, SEEK_END */
    off_t l_start;   /* Starting offset for lock */
    off_t l_len;     /* Number of bytes to lock */
    pid_t l_pid;     /* PID of process blocking our lock
                       (set by F_GETLK and F_OFD_GETLK) */
};
```
![APUE-lock-rules](http://www.rainbowch.net/resource/APUE-lock-rules.png)
![APUE-fcntl-cmd](http://www.rainbowch.net/resource/APUE-fcntl-cmd.png)
### I/O多路转接
```c
#include <sys/select.h>
// 多路转接
int select(imt maxfdp1, fd_set *restrict readfds, fd_set *restrict writefds, fd_set *restrict exceptfds,
          struct timeval *restrict tvptr) /* 'readfds'是函数中最大的文件描述符加1，返回准备就绪的描述符数目 */
void FD_ISSET(int fd, fd_set *fdset)
void FD_CLR(int fd, fd_set *fdset)
void FD_SET(int fd, fd_set *fdset)
void FD_ZERO(fd_set *fdset)
// 附带信号屏蔽字的'select'
int pselect(imt maxfdp1, fd_set *restrict readfds, fd_set *restrict writefds, fd_set *restrict exceptfds,
          const struct timespec *restrict tvptr, const sigset_t *restrict sigmask) /* 'readfds'是函数中最大的文件描述符加1，返回准备就绪的描述符数目 */
#include <poll.h>
int poll(struct pollfd fdarray[], nfds_t nfds, int timeout) /* 'timeout'以毫秒为单位，返回准备就绪的描述符数目 */
struct pollfd {
    int fd;
    short events;
    short revents;
}
```
![APUE-poll-events](http://www.rainbowch.net/resource/APUE-poll-events.png)
### 异步I/O
```c
#include <aio.h>
struct aiocb {
    /* The order of these fields is implementation-dependent */

    int             aio_fildes;     /* File descriptor */
    off_t           aio_offset;     /* File offset */
    volatile void  *aio_buf;        /* Location of buffer */
    size_t          aio_nbytes;     /* Length of transfer */
    int             aio_reqprio;    /* Request priority */
    struct sigevent aio_sigevent;   /* Notification method */
    int             aio_lio_opcode; /* Operation to be performed;
                                  lio_listio() only */

    /* Various implementation-internal fields not shown */
};

struct sigevent {
    int    sigev_notify;  /* Notification method: 'SIGEV_NONE'、'SIGEV_SIGNAL'、'SIGEV_THREAD' */
    int    sigev_signo;   /* Notification signal */
    union sigval sigev_value;
                         /* Data passed with notification */
    void (*sigev_notify_function) (union sigval);
                         /* Function used for thread
                            notification (SIGEV_THREAD) */
    void  *sigev_notify_attributes;
                         /* Attributes for notification thread
                            (SIGEV_THREAD) */
    pid_t  sigev_notify_thread_id;
                         /* ID of thread to signal
                            (SIGEV_THREAD_ID); Linux-specific */
};

union sigval {            /* Data passed with notification */
    int     sival_int;    /* Integer value */
    void   *sival_ptr;    /* Pointer value */
};
// 异步读写
int aio_read(struct aiocb *aiocb)
int aio_write(struct aiocb *aiocb)
// 保证数据同步到持久化存储中
int aio_fsync(int op, struct aiocb *aiocb) /* 'op'为'O_DSYNC'时相当于'fdatasync', 为O_SYNC'时相当于'fsync' */
// 获得异步读写的完成状态
int aio_error(const struct aiocb *aiocb) /* 0:完成  -1:失败  'EINPROGRESS':仍在等待 */
// 在异步I/O完成后调用，获得异步I/O中read/write/fsync操作对应的返回值
ssize_t aio_return(const struct aiocb *aiocb)
// 阻塞进程，等待异步I/O完成
int aio_suspend(const struct aiocb *const list[], int nent, const struct timespec *timeout)
// 尝试取消正进行的异步I/O操作
int aio_cancel(int fd, struct aiocb *aiocb) /* 返回值：'AIO_ALLDONE'、'AIO_CANCELED'、'AIO_NOTCANCELD'、-1 */
// 提交一系列异步I/O请求
// int lio_listio(int mode, struct aiocb *restrict const list[restrict], int nent, struct sigevent *restrict sigev) /* 'mode'为'LIO_WAIT'时同步执行I/O,为'LIO_NOWAIT'时异步执行I/O，'sigev'中指定的异步通知会在所有的I/O操作完成后发送 */
```

### 散布读与聚集写
```c
#include <sys/uio.h>
// 散步读
ssize_t readv(int fd, const struct iovec *iov, int iovcnt)
// 聚集写
ssize_t writev(int fd, const struct iovec *iov, int iovcnt)
struct iovec {
    void   *iov_base;   /* starting address of buffer */
    size_t iov_len;     /* size of buffer */
}
```
### 内存映射I/O
```c
#include <sys/mmanl.h>
// 将磁盘文件映射到内存中的映射区中，使得在映射区中读写相当于读写磁盘文件(读写操作会自动反馈到文件)
void *mmap(void *addr, size_t len, int prot, int flag, int fd, off_t off) /* 'addr'为映射区的其实地址，一般为0，表示由系统选择，'prot'指定连映射区的保护要求，返回值：映射区的起始地址 */
// 更改现有映射区的权限
int mprotect(void *addr, size_t len, int prot)
// 将页冲洗到被映射的文件
int msync(void *addr, size_t len, int flags) /* 'flags'为'MS_ASYNC'不阻塞冲洗，'MS_SYNC'阻塞冲洗，'MS_INVALIDATE'丢弃没有同步的页 */
/ 关闭映射区
int munmap(void *addr, size_t len) /* 注意关闭映射区使用的文件描述符并不解除映射区 */
```
![APUE-mmap-prot](http://www.rainbowch.net/resource/APUE-mmap-prot.png)
![APUE-mmap-flag](http://www.rainbowch.net/resource/APUE-mmap-flag.png)

## 进程间通信
## 管道
```c
#include <unistd.h>
// 创建管道
int pipe(int fd[2]) /* fd[0]读而打开，fd[1]写而打开，fd[1]的输出示fd[0]的输入，写管道的字节数不能大于'PIPE_BUF' */
#include <stdio.h>
// pipe+fork+exec
FILE *popen(const char *cmdstring, const char *type) /* 'cmdstring'是传递给shell的命令，'type'为'r'或'w'，返回值：标准I/O的文件指针，
                                                        为'r'时连接到'cmdstring'的标准输出，为'w'时连接到标准输入 */
// 关闭标准I/O流，等待命令结束，然后返回shell的终止状态
int pclose(FILE *fp)
```

## FIFO(命名管道)
```c
#include <sys/stat.h>
// 创建FIFO
int mkfifo(const char *path, mode_t mode) /* 'mode'取值与'open'函数中的'mode'值相同, 在写FIFO文件时，尽量考虑一次写低于'PIPE_BUF'字节量的数据 */
int mkfifoat(int fd, const char *path, mode_t mode)
```

## XSI IPC
```c
// 由一个路径名和项目ID产生一个键
#include <sys/ipc.h>
key_t ftok(const char *path, int id) /* 'id'只被使用低8位 */
#include <sys/msg.h>
// 创建消息队列
int msgget(key_t key, int flag) /* 返回消息队列ID */
// 类似于针对消息队列的'ioctl'函数
int msgctl(int msqid, int cmd, struct msqid_ds *buf)
// 将数据放到消息队列中
int msgsnd(imt msqid, const void *ptr, size_t nbytes, int flag) /* 'flag'为'IPC_NOWAIT'时消息队列满时非阻塞出错返回'EAGAIN'，否则等待队列有空间或队列被删除或捕捉到一个信号 */
// 从队列中取消息
ssize_t msgrcv(int msqid, void *ptr, size_t nbytes, long type, int flag) /* 'type'指定取出哪一种消息，0:第一个，>0:类型为'type'的消息，<0:类型小于等于'|type|'的类型值最小的消息 */
#include <sys/sem.h>
// 创建信号量
int semget(key_t key, int nsems, int flag) /* 'nsems'时集合中的信号量数, 返回信号量ID */
// 对信号量的多种操作
int semctl(int semid, int semnum, int cmd, ... /* union semnu arg */)
#include <sys/shm.h>
// 创建一个共享存储段
int shmget(key_t key, size_t size, int flag)
// 对共享存储段进行多种操作
int shmctl(int shmid, int cmd, struct shmid_ds *buf)
// 将共享存储段连接到进程的地址空间
void *shmat(int shmid, const void *addr, int flag) /* 返回共享存储段链接的实际地址 */
// 与共享存储段分离连接
int shmdt(const void *addr)
// POSIX信号量:未命名信号量、命名信号量
#include <semaphore.h>
// 新建命名信号量或使用现有信号量
sem_t *sem_open(const char *name, int oflag, .. */ mode_t mode, unsigned int value */) /* 返回信号量指针 */
// 释放与信号量相关的资源
int sem_close(sem_t *sem)
// 销毁命名信号量
int sem_unlink(const char *name)
// 减1操作
int sem_trywait(sem_t *sem)
int sem_wait(sem_t *sem)
#include <time.h>
int sem_timedwait(sem_t *restrict sem const struct timespec *restrict tsptr)
// 加1操作
int sem_post(sem_t *sem)
// 创建未命名的信号量
int sem_init(sem_t *sem, int pshared, unsigned int value)
// 销毁未命名信号量
int sem_destroy(sem_t *sem)
// 获取信号量的值
int sem_getvalue(sem_t *restrict sem, int *restrict valp)
```
![APUE-XSI-IPC-access-mode](http://www.rainbowch.net/resource/APUE-XSI-IPC-access-mode.png)

## 网络IPC：套接字
### 套接字描述符
```c
#include <sys/socket.h>
// 创建套接字描述符
int socket(int domain, int type, int protocol)
// 关闭套接字的I/O
int shutdown(int sockfd, int how) /* 'how'为'SHUT_RD'(关闭读端)、'SHUTWR'(关闭写端)、'SHUTRDWR'(关闭读写端)
```
![APUE-socket-domain](http://www.rainbowch.net/resource/APUE-socket-domain.png)
![APUE-socket-type](http://www.rainbowch.net/resource/APUE-socket-type.png)
![APUE-socket-protocol](http://www.rainbowch.net/resource/APUE-socket-protocol.png)

### 字节序
```c
// 字节排序函数
#include <netinet/in.h>
// 16bits主机序转换为网络序
uint16_t htons(uint16_t host16bitvalue)
// 32bits网络序转换为主机序
uint32_t htonl(uint32_t host32bitvalue)
// 16bits主机序转换为网络序
uint16_t ntohs(uint16_t net16bitvalue)
// 32bits网络序转换为主机序
uint32_t ntohl(uint32_t net32bitvalue)
```

### 地址格式
```c
#include <netinet/in.h>
struct in_addr {
    in_addr_t s_addr; /* network byte ordered */
}

// IPv4套接字
struct sockaddr_in {
    uint8_t sin_len;
    sa_family_t sin_family; /* AF_INET, AF_INET6 */
    in_port_t sin_port;
    struct in_addr sin_addr; /* sin_addr.s_addr = htonl(INADDR_ANY) */
    char sin_zero[8];
}

// IPv6套接字
struct sockaddr_in6 {
    uint8_t sin6_len;
    sa_family_t sin6_family;
    in_port_t sin6_port;

    uint32_t sin6_flowinfo;
    struct in6_addr sin6_addr; /* sin6_addr = in6addr_any */
    uint32_t sin6_scope_id;
}

// 通用套接字
#include <sys/socket.h>
struct sockaddr {
    uint8_t sa_len;
    sa_family_t sa_family;
    char sa_data[14];
}

// 新的通用套接字
struct sockaddr_storage {
    uint8_t ss_len;
    sa_family_t ss_family;
    ... /* enough large */
}

// 字符串转网络字节序的二进制值
int inet_pton(int family, const char *strptr, void *addrptr)
// 网络字节序的二进制值转字符串
const char *inet_ntop(int family, const void *addrptr, char *strptr, size_t len) // where 'family' is AF_INET AF_INET6
// 'len'的建议值
#include <netinet/in.h>
#define INET_ADDRSTRLEN 16
#define INET6_ADDRSTRLEN 46
```

### 地址查询
```c
#include <netdb.h>
// 获取主机信息
struct hostent *gethostbyname(const char *name)
void sethostent(int stayopen)
// 关闭主机数据库文件
void endhostent(void)
struct hostent {
    char  *h_name;            /* official name of host */
    char **h_aliases;         /* alias list */
    int    h_addrtype;        /* host address type */
    int    h_length;          /* length of address */
    char **h_addr_list;       /* list of addresses */
}
// 网络地址项相关操作
struct netent *getnetent(void);
struct netent *getnetbyname(const char *name);
struct netent *getnetbyaddr(uint32_t net, int type);
void setnetent(int stayopen);
void endnetent(void);
struct netent {
    char      *n_name;     /* official network name */
    char     **n_aliases;  /* alias list */
    int        n_addrtype; /* net address type */
    uint32_t   n_net;      /* network number */
}
// 协议项相关操作
struct protoent *getprotoent(void);
struct protoent *getprotobyname(const char *name);
struct protoent *getprotobynumber(int proto);
void setprotoent(int stayopen);
void endprotoent(void);
struct protoent {
    char  *p_name;       /* official protocol name */
    char **p_aliases;    /* alias list */
    int    p_proto;      /* protocol number */
}
// 服务项相关操作
struct servent *getservent(void);
struct servent *getservbyname(const char *name, const char *proto);
struct servent *getservbyport(int port, const char *proto);
void setservent(int stayopen);
void endservent(void);
struct servent {
    char  *s_name;       /* official service name */
    char **s_aliases;    /* alias list */
    int    s_port;       /* port number */
    char  *s_proto;      /* protocol to use */
}
#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>
// 将主机名和一个服务名映射到一个地址
int getaddrinfo(const char *node, const char *service,
               const struct addrinfo *hints,
               struct addrinfo **res);
// 释放addrinfo链表结构
void freeaddrinfo(struct addrinfo *res);
// 返回'getaddrinfo'错误消息
const char *gai_strerror(int errcode);
struct addrinfo {
    int              ai_flags;
    int              ai_family;
    int              ai_socktype;
    int              ai_protocol;
    socklen_t        ai_addrlen;
    struct sockaddr *ai_addr;
    char            *ai_canonname;
    struct addrinfo *ai_next;
};
// 将一个地址装换成一个主机名和一个服务名
int getnameinfo(const struct sockaddr *addr, socklen_t addrlen,
               char *host, socklen_t hostlen,
               char *serv, socklen_t servlen, int flags);
```
![APUE-addrinfo-ai_flags](http://www.rainbowch.net/resource/APUE-addrinfo-ai_flags.png)
![APUE-getnameinfo-flags](http://www.rainbowch.net/resource/APUE-getnameinfo-flags.png)

### 套接字与地址关联
```c
#include <sys/socket.h>
// 将套接字与地址关联
int bind(int sockfd, const struct sockaddr *addr, socklen_t len)
// 获取绑定到套接字上的地址
int getsockname(int sockfd, struct sockaddr *restrict addr, socklen_t *restrict alenp)
// 获得已连接套接字对等方的地址
int getpeername(int sockfd, struct sockaddr *restrict addr, socklen_t *restrict alenp)
```

### 建立链接
```c
// 建立连接
#include <sys/socket.h>
int connect(int sockfd, const struct sockaddr *addr, socklen_t len)
// 服务器宣告愿意接受的连接请求数
int listen(int sockfd, int backlog)
// 获得连接请求并建立连接
int accept(int sockfd, struct sockaddr *restrict addr, socklen_t *restrict len) /* 返回与客户端相连的新的套接字 */
```

### 数据传输
```c
#include <sys/socket.h>
// 在已连接的套接字上发送数据, 类似于'write'
ssize_t send(int sockfd, const void *buf, size_t nbytes, int flags) */ 返回发送的字节数 */
// 作用在无连接套接字上的'send'
ssize_t sendto(int sockfd, const void *buf, size_t nbytes, int flags, const struct sockaddr *destaddr, socklen_t destlen)
// 多重缓存区的'send'
ssize_t sendmsg(int sockfd, const struct msghdr *msg, int flags)
struct msghdr {
    void         *msg_name;       /* Optional address */
    socklen_t     msg_namelen;    /* Size of address */
    struct iovec *msg_iov;        /* Scatter/gather array */
    size_t        msg_iovlen;     /* # elements in msg_iov */
    void         *msg_control;    /* Ancillary data, see below */
    size_t        msg_controllen; /* Ancillary data buffer len */
    int           msg_flags;      /* Flags on received message */
};
// 接受数据，与'read'类似
ssize_t recv(int sockfd, void *buf, size_t nbytes, int flags) /* 返回数据的字节长度 */
// 接受数据，并定位发送者
ssize_t recvfrom(int sockfd, void *buf, size_t nbytes, int flags, struct sockaddr *restrict addr, socklen_t *restrict addrlen) /* 数据发送方的地址会存储到'addr'中 */
// 多重缓存区的'recv'
ssize_t recvmsg(int sockfd, struct msghdr *msg, int flags)
```
![APUE-send-flags](http://www.rainbowch.net/resource/APUE-send-flags.png)
![APUE-recv-flags](http://www.rainbowch.net/resource/APUE-recv-flags.png)
![APUE-recvmsg-flags](http://www.rainbowch.net/resource/APUE-recvmsg-flags.png)

### 套接字选项
```c
#include <sys/socket.h>
// 设置套接字选项
int setsockopt(int sockfd, int level, int option, const void *val, socklen_t len) /* 'level'是选项应用的协议 */
int getsockopt(int sockfd, int level, int option, void *restrict val, socklen_t *restrict lenp) /* 'level'是选项应用的协议 */
```
![APUE-sockopt](http://www.rainbowch.net/resource/APUE-sockopt.png)

### 带外数据(out-of-band data)
```c
// 设置套接字的所有权进程
fcntl(scokfd, F_SETOWN, pid)
// 获得套接字的所有权进程ID
fcntl(scokfd, F_GETOWN, pid)
// 判断下一个字节是否是紧急标志处
#include <sys/socket.h>
int sockatmark(int sockfd) /* 返回1:在标记处，返回0不在 */
```

## 高级进程间通信
```c
// 创建一对无名的互相连接的套接字
#include <sys/socket.h>
int socketpair(int domain, int type, int protocol, int sockfd[2])
// 可使用'bind'函数为无名套接字绑定'S_IFSOCK'类型的文件, 此文件不会自动删除，需要手动解除链接
#include <sys/un.h>
struct sockaddr_un {
    sa_family_t sun_family;    /* AF_UNIX */
    char        sun_path[108]; /* pathname */
}
// 计算成员从结构体开始处的偏移量
#include <stddef.h>
#define offsetof(TYPE, MEMBER) ((int)&((TYPE *)0)->MEMBER)
```

## 规范的命令行选项
```c
#include <unistd.h>
// 获取命令行选项
int getopt(int argc, char *const argv[], const char *options) /* 返回下一个选项字符，选项处理完返回-1, 遇到无效选项或缺少选项返回'?' */
extern int optind, opterr, optopt
extern char *optarg
```
![APUE-getopt-variable](http://www.rainbowch.net/resource/APUE-getopt-variable.png)

## 终端I/O
```c
#include <termios.h>
struct termios {
    tcflag_t c_iflag;      /* input modes */
    tcflag_t c_oflag;      /* output modes */
    tcflag_t c_cflag;      /* control modes */
    tcflag_t c_lflag;      /* local modes */
    cc_t     c_cc[NCCS];   /* special characters */
}
int tcgetattr(int fd, struct termios *termios_p);
int tcsetattr(int fd, int optional_actions, const struct termios *termios_p);
int tcsendbreak(int fd, int duration);
int tcdrain(int fd);
int tcflush(int fd, int queue_selector);
int tcflow(int fd, int action);
void cfmakeraw(struct termios *termios_p);
speed_t cfgetispeed(const struct termios *termios_p);
speed_t cfgetospeed(const struct termios *termios_p);
int cfsetispeed(struct termios *termios_p, speed_t speed);
int cfsetospeed(struct termios *termios_p, speed_t speed);
int cfsetspeed(struct termios *termios_p, speed_t speed);
```
![APUE-termios-c_cflag](http://www.rainbowch.net/resource/APUE-termios-c_cflag.png)
![APUE-termios-c_iflag](http://www.rainbowch.net/resource/APUE-termios-c_iflag.png)
![APUE-termios-c_lflag](http://www.rainbowch.net/resource/APUE-termios-c_lflag.png)
![APUE-termios-c_oflag](http://www.rainbowch.net/resource/APUE-termios-c_oflag.png)
![APUE-termios-functions](http://www.rainbowch.net/resource/APUE-termios-functions.png)
![APUE-termios-special-characters1](http://www.rainbowch.net/resource/APUE-termios-special-characters1.png)
![APUE-termios-special-characters2](http://www.rainbowch.net/resource/APUE-termios-special-characters2.png)
