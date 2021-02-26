# IPv6 Sockets Programming Notes

## `struct sockaddr_in6` is bigger than `struct sockaddr`

BSD Sockets API is not type-safe w.r.t. socket address structs,
probably because it predates function signature checking in C.
We often need to cast a `struct sockaddr_in*` or `struct sockaddr_in6*` to `struct sockaddr*`, like:

```
// Sockets API often takes struct sockaddr*, which could point to
// sockaddr_in, sockaddr_in6, or sockaddr_un.

int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

```
// IPv4
struct sockaddr_in listen_addr = {
  .sin_family      = AF_INET,
  .sin_port        = htons(8000),
  .sin_addr.s_addr = htonl(INADDR_LOOPBACK),
};

int sockfd = socket(AF_INET, SOCK_STREAM, 0);
if (bind(sockfd, (struct sockaddr *) &listen_addr, sizeof listen_addr) < 0) {
  perror("bind");
}
```

```
// IPv6
struct sockaddr_in6 addr6 = {
  .sin6_family  = AF_INET6,
  .sin6_port    = htons(8000),
  .sin6_addr    = in6addr_any,
};

int sockfd = socket(AF_INET6, SOCK_STREAM, 0);
if (bind(sockfd, (struct sockaddr *) &addr6, sizeof addr6) < 0) {
  perror("bind");
}
```

On platforms I tested, `sizeof(struct sockaddr) == sizeof(struct sockaddr_in) == 16`,
while `sizeof(struct sockaddr_in6) == 28`.
So sockets functions that take `struct sockaddr*` as input parameter usually work fine, e.g.
`bind()`, `connect()`, `sendto()`.

However, be careful about output parameter in `accept()`, `recvfrom()`, `getpeername()`, and `getsockname()`.
Don't use `struct sockaddr` to hold the result, unless you know it's AF_INET not AF_INET6.

<div class="warning">
Wrong UDP echo server for IPv6
```
void udp_echo(int sockfd)
{
  struct sockaddr peerAddr;  // *** Can't hold sockaddr_in6
  socklen_t addrLen;
  char message[1024];

  while (true) {
    addrLen = sizeof peerAddr;
    bzero(&peerAddr, sizeof peerAddr);
    ssize_t nr = recvfrom(sockfd, message, sizeof message, 0, &peerAddr, &addrLen);
    // peerAddr is truncated in IPv6, so message won't be echoed back to the sender.
    if (nr >= 0) {
      ssize_t nw = sendto(sockfd, message, nr, 0, &peerAddr, addrLen);
    }
  }
}
```
</div>

`strace(1)` output:
```
recvfrom(3, "hello", 1024, 0, {sa_family=AF_INET6, sa_data="\222R\0\0\0\0\0\0\0\0\0\0\0\0"}, [16->28]) = 5
sendto(3, "hello", 5, 0, {sa_family=AF_INET6, sin6_port=htons(37458), sin6_flowinfo=htonl(0), inet_pton(AF_INET6, "::fa3b:3860:0:0", &sin6_addr), sin6_scope_id=539754}, 28) = 5
```
Note that the address was truncated `[16->28]` in `recvfrom()`.


<div class="warning">
Even worse, an address violation could happen when reading a `struct sockaddr` as `struct sockaddr_in6`.
```
void printAddress(struct sockaddr* addr)
{
  char buf[INET6_ADDRSTRLEN];
  if (peerAddr.sa_family == AF_INET) {
    // ...
  } else if (peerAddr.sa_family == AF_INET6) {
    struct sockaddr_in6* addr6 = (struct sockaddr_in6*)&peerAddr;  // ***
    if (inet_ntop(AF_INET6, &addr6->sin6_addr, buf, sizeof buf)) {
      printf("[%s]:%u\n", buf, ntohs(addr6->sin6_port));
    }
  }
}

void printPeerNameWrong(int sockfd)
{
  struct sockaddr peerAddr;
  socklen_t addrlen = sizeof peerAddr;
  if (getpeername(sockfd, &peerAddr, &addrlen) < 0) {
    perror("getpeername");
    return;
  }
  // !!! should check addrlen <= sizeof peerAddr.
  printAddress(&peerAddr);
}

```
</div>


AddressSanitizer output:
```
==3735==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7ffd8e5a3380 at pc 0x7f0e484164c2 bp 0x7ffd8e5a3240 sp 0x7ffd8e5a29f0
READ of size 16 at 0x7ffd8e5a3380 thread T0
    #0 0x7f0e484164c1 in __interceptor_inet_ntop ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:2478
    #1 0x5595a07923d1 in printAddress (/home/schen/cpp/a.out+0x13d1)
    #2 0x5595a0792728 in printPeerNameWrong (/home/schen/cpp/a.out+0x1728)
    #3 0x5595a0792b1c in main (/home/schen/cpp/a.out+0x1b1c)
    #4 0x7f0e48237d09 in __libc_start_main ../csu/libc-start.c:308
    #5 0x5595a07921e9 in _start (/home/schen/cpp/a.out+0x11e9)

Address 0x7ffd8e5a3380 is located in stack of thread T0 at offset 80 in frame
    #0 0x5595a0792645 in printPeerNameWrong (/home/schen/cpp/a.out+0x1645)

  This frame has 2 object(s):
    [48, 52) 'addrlen' (line 37)
    [64, 80) 'peerAddr' (line 36) <== Memory access at offset 80 overflows this variable
SUMMARY: AddressSanitizer: stack-buffer-overflow ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:2478 in __interceptor_inet_ntop
```

This stack buffer overflow is not detected by Valgrind, instead, the program prints some
truncated address like `2601:647:4500:32bd::` when it should print `2601:647:4500:32bd:8a51:xxff:fexx:xxxx`.

In both cases above, `struct sockaddr_storage` should be used instead.

A `reinterpret_cast` is usually needed in C++, or two `static_cast`
from `struct sockaddr_in*` to `void*`, then to `struct sockaddr*`, like this:
`static_cast<sockaddr*>(static_cast<void*>(&addr))`.
New functions like `inet_pton` and `inet_ntop` use `void*` for socket addresses,
save us some casting.
Also in C++, pass-by-reference (`struct sockaddr&`) vs. pass-by-value,
the latter may be a victim of object-slicing problem.

## Host environment

1. IPv4 only
    * `socket(AF_INET, ...)` succeeds
    * `socket(AF_INET6, ...)` fails
2. IPv6 only
    * `socket(AF_INET, ...)` fails
    * `socket(AF_INET6, ...)` succeeds
3. Dual-stack
    * `socket(AF_INET, ...)` succeeds
    * `socket(AF_INET6, ...)` succeeds

## Client

Usually easy to write protocol-independent code.

1. parse IP address & port from text, select `sockaddr_in` or `sockaddr_in6` based on address format.
1. create socket fd for saddr.sa_family.
1. connect to saddr
1. read/write as usual.

## Server

1. IPv4 only, listen on 0.0.0.0:8000, serves IPv4 clients only.
1. IPv6 dual-stack, listen on [::]:8000, serves both IPv4 and IPv6 clients. IPv4 clients show up as IPv4-mapped address, like `::ffff:10.0.0.118`.
1. IPv6 only, listen on [::]:8000, turn on `IPV6_V6ONLY`, serves IPv6 clients only.
1. Two sockets, one listens on [::]:8000, turn on `IPV6_V6ONLY`, another listens on 0.0.0.0:8000.
   Serves IPv6 and IPv4 clients, respectively.`sshd` and `nginx` do this by default.

System wide `IPV6_V6ONLY` on Linux:

* sysctl `net.ipv6.bindv6only`
* `/proc/sys/net/ipv6/bindv6only`

Linux has it off by default, but see ref below for different opinion from OpenBSD.

## `read(2)` vs. `recv(2)`

## References

* [RFC3493](https://tools.ietf.org/html/rfc3493) Basic Socket Interface Extensions for IPv6, 2020/02
* [lwn688462](https://lwn.net/Articles/688462/) Should distributors disable IPv4-mapped IPv6?
* [UNP3e](http://unpbook.com/) _Unix Network Programming 3/e_, Chapter 12.

