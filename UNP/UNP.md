# UNP Note

## Preface
同上一篇Unix高级环境编程篇一样，我认为这种类型的书籍还是应该把重心放在Coding上面，所以本文还是只提供API的笔记，如果读者有疑惑的话，建议直接查看书籍原文(原文写的很棒)，或通过搜索引擎查阅相关资料。

## Socket
```c
#include <netinet/in.h>
struct sockaddr_in {
    uint8_t sin_len;
    sa_family_t sin_family; /* AF_INET, AF_INET6 */
    in_port_t sin_port;
    struct in_addr sin_addr; /* sin_addr.s_addr = htonl(INADDR_ANY) */
    char sin_zero[8];
}

struct sockaddr_in6 {
    uint8_t sin6_len;
    sa_family_t sin6_family;
    in_port_t sin6_port;

    uint32_t sin6_flowinfo;
    struct in6_addr sin6_addr; /* sin6_addr = in6addr_any */
    uint32_t sin6_scope_id;
}

struct sockaddr_storage {
    uint8_t ss_len;
    sa_family_t ss_family;
    ... /* enough large */
}

#include <sys/socket.h>
struct sockaddr {
    uint8_t sa_len;
    sa_family_t sa_family;
    char sa_data[14];
}
```

```c
#include <netinet/in.h>
uint16_t htons(uint16_t host16bitvalue)
uint32_t htonl(uint32_t host32bitvalue)
uint16_t ntohs(uint16_t net16bitvalue)
uint32_t ntohl(uint32_t net32bitvalue)
```

```c
#include <string.h>
void bzero(void *dest, size_t nbytes)
void bcopy(const void *src, void *dest, size_t nbytes)
int bcmp(const void *ptr1, const void *ptr2, size_t nbytes)

void *memset(void *dest, int c, size_t len)
void *memcpy(void *dest, const void *src, size_t nbytes)
int memcmp(const void *ptr1, const void *ptr2, size_t nbytes)
```

```c
#include <arpa/inet.h>
int inet_aton(const char *strptr, struct in_addr *addptr)
in_addr_t inet_addr(const char *strptr) //depreture
char *inet_ntoa(struct in_addr inaddr)

int inet_pton(int family, const char *strptr, void *addrptr)
const char *inet_ntop(int family, const void *addrptr, char *strptr, size_t len)
where 'family' is AF_INET AF_INET6
#include <netinet/in.h>
#define INET_ADDRSTRLEN 16
#define INET6_ADDRSTRLEN 46
```

## Tcp Socket

```c
#include <sys/socket.h>
int socket(int family, int type, int protocol)
int connect(int sockfd, const struct sockaddr *servaddr, socklen_t addrlen)
int bind(int sockfd, const struct sockaddr *myaddr, socklen_t addrlen)
int listen(int sockfd, int backlog)
int accept(int sockfd, struct sockaddr *cliaddr, socklen_t addrlen)
int getsockname(int sockfd, struct sockaddr *localaddr, socklen_t addrlen)
int getpeername(int sockfd, struct sockaddr *peeraddr, socklen_t addrlen)

#include <unistd.h>
int close(int sockfd)
```

```c
#include <sys/select.h>
#include <sys/time.h>
int select(int maxfdpl, fd_set *readset, fd_set *writeset, fd_set *exceptset, const struct timeval *timeout)

#include <sys/socket.h>
int shutdown(int sockfd, int howto)
where 'howto' is SHUT_RD SHUT_WR SHUT_RDWR

#include <sys/select.h>
#include <signal.h>
#include <time.h>
int pselect(int maxfdpl, fd_set *readset, fd_set *writeset, fd_set *exceptset, const struct timespec *timeout, const sigset_t *sigmask)

#include <poll.h>
int poll(struct pollfd *fdarray, unsigned long nfds, int timeout)
struct pollfd {
    int fd;
    short events;
    short revents;
}
```

## Socket Options

```c
#include <sys/socket.h>
int getsockopt(int sockfd, int level, int optname, void *optval, socklen_t *optlen)
int setsockopt(int sockfd, int level, int optname, void *optval, socklen_t optlen)
```
where 'optname' is:
1. 通用套接字选项
~~~
- SO_BROADCAST  开启或禁止广播能力
- SO_DEBUG      TCP保留详细跟踪消息
- SO_DONTROUTE  出境分组绕过正常路由机制
- SO_ERROR      套接字待处理错误
- SO_KEEPALIVE  2小时内没有数据交换，TCP发送keep-alive probe
- SO_LINGER     更改close调用的默认处理方式
- SO_OOBINLINE  带外数据留在正常的输入队列里
- SO_RCVBUF SO_SNDBUF   更改缓冲区大小
- SO_RCVLOWAT SO_SNDLOWAT   设置低水位标记
- SO_RCVTIMEO SO_SNDTIMEO   设置套接字超时值
- SO_REUSEADDR SO_REUSEPORT 允许重用端口
- SO_TYPE       套接字类型
- SO_USELOOPBACK    相应的套接字接收在其上发送的任何数据报的副本
~~~
2. IPv4选项
~~~
- IP_HDRINCL    要求手动构造IP首部
- IP_OPTIONS    设置IP选项
- IP_RECVDSTADDR    收到的UDP数据报的目的IP地址由recvmsg函数作为辅助数据返回
- IP_RECVIF    收到的UDP数据报的接收接口索引由recvmsg函数作为辅助数据返回
- IP_TOS    设置IP服务类型字段
- IP_TTL    设置IP发送的单播分组上的默认TTL值
~~~
3. ICMPv6套接字选项
~~~
- ICMP6_FILTER  允许设置256个由原始套接字传递给应用程序的ICMPv6消息类型
~~~
4. IPv6套接字选项
~~~
- IPV6_CHECKSUM 非负则计算校验和，-1（默认）不计算
- IPV6_DONTFRAG 不分片
- IPV6_NEXTHOP  指定下一跳地址
- IPV6_PATHMTU  只能获取，当前MTU
- IPV6_RECVDSOPTS    收到的IPv6目的地选项由recvmsg函数作为辅助数据返回
- IPV6_RECVHPLIMIT    收到的跳限字段由recvmsg函数作为辅助数据返回
- IPV6_RECVHOPOPTS    收到的IPv6步跳选项由recvmsg函数作为辅助数据返回
- IPV6_RECVPATHMTU    某条线路MTU发生变化由recvmsg函数作为辅助数据返回
- IPV6_RECVPKTINFO    收到的IPv6目的IP地址和到达接口索引由recvmsg函数作为辅助数据返回
- IPV6_RECVRTHDR    收到的IPv6路由首部由recvmsg函数作为辅助数据返回
- IPV6_RECVTCLASS    收到的IPv6流通类别由recvmsg函数作为辅助数据返回
- IPV6_UNICAST_HOPS    类似于IP_TTL
- IPV6_USE_MIN_MTU  MTU是否执行：1不执行，0执行，-1只对单播执行
- IPV6_V6ONLY   限制套接字只执行IPv6通信
- IPV6_XXX  修改IPv6首部选项
~~~
5. TCP套接字选项
~~~
- TCP_MAXSEG    获取和设置MSS
- TCP_NODELAY   禁止Nagle算法
~~~

## UDP Socket
```c
#include <sys/socket.h>
ssize recvfrom(int sockfd, void *buff, size_t nbytes, int flags, struct sockaddr *from, socklen_t *addlen)
ssize sendto(int sockfd, void *buff, size_t nbytes, int flags, const struct sockaddr *to, socklen_t *addlen)
```

## DNS
```c
#include <netdb.h>
struct hostent *gethostbyname(const char *hostname) /* only fuction for IPv4 */
struct hostent {
    char *h_name;
    char **h_aliases;
    int h_addrtype;
    int h_length;
    char **h_addr_list;
}
struct hostent *gethostbyaddr(const char *addr, socklen_t len, int family) /* only fuction for IPv4 */
struct servent *getservbyname(const char *servname, const char *protoname)
struct servent {
    char *s_name;
    char *s_aliases;
    int s_port; /* network byte order */
    char *s_proto;
}
struct servent *getservbyport(int port, const char *protoname)

int getaddrinfo(const char *hostname, const char *service,
		const struct addrinfo *hints, struct addrinfo **result)
struct addrinfo {
    int ai_flags;
    int ai_family;
    int ai_socktype;
    int ai_protocol;
    int ai_addrlen;
    char *ai_canonname;
    struct sockaddr *ai_addr;
    struct addrinfo *ai_next;
}
```
