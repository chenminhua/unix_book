文件描述符

```c
#include <fcntl.h>
int open(const char *path, int oflag,...);
int openat(int fd, const char *path, int oflag,...);
int creat(const char *path, mode_t mode);

#include <unistd.h>
int close(int fd);
off_t lseek(int fd, off_t offset, int whence);
int read(int fd, void *buf, size_t nbytes);
```

大多数文件系统都会采用某种预读技术，当检测到正在进行顺序读取，系统会试图读入比应用所要求的更多数据。

思考题：如果两个进程同时打开同一个文件，并且开始写，会发生什么?

假如你没有使用O_APPEND选项打开文件，此时每个进程都有一个文件表项，但是共享一个v节点表项。假设现在文件有1500字节，此时A进程调用了lseek，将进程A的该文件当前偏移量设置为1500字节。然后内核切换进程，进程B也将偏移量设置在1500字节处，B向文件写入100字节，所以偏移量来到了1600。此时进程再次切换回了A，A不知道偏移量来到了1600，因此它开始从1500位置写，就覆盖了B写的东西。
问题出在先定位到文件末尾在写，这是两个分开的系统调用。解决这个问题的方法是将这两个操作合并成一个原子操作。只需要在打开文件时使用O_APPEND选项即可。

```c
#include <unistd.h>
// 复制文件描述符
int dup(int fd);
int dup2(int fd, intfd2);
```

## sync, fsync, fdatasync 缓冲区同步到磁盘
内核中设有缓冲区，大多数磁盘IO都在缓冲区进行，当我们向文件写入东西时会先写到缓冲区，然后排入队列，晚些时候再写入磁盘(delayed write)。为了保证磁盘上实际文件系统与缓冲区内容一致，Unix提供了下面三个函数。

```c
#include<unistd.h>
int fsync(int fd);
int fdatasync(int fd);
void sync(void);
```

## fcntl 改变打开文件的属性
```c
#include<fcntl.h>
int fcntl(int fd, int cmd, ...);
复制文件描述符
获取、设置文件描述符
获取、设置文件状态标志
获取、设置异步IO所有权
获取、设置记录锁
```

14章会用fcntl记录锁

## ioctl
```c
#include <sys/ioctl.h>
#inclued <termios.h>   // 终端io需要此头文件
int ioctl(int fd, int request, ...);

IO杂物箱，终端IO是ioctl使用最多的地方。18，19章会将ioctl用于终端设备。
```
