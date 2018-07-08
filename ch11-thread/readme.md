每个线程都包含有表示执行环境所必需的信息，其中包括线程ID，一组寄存器值，栈，调度优先级和策略，信号屏蔽字，errno变量，线程私有数据。进程的所有信息对该进程的所有线程都是共享的。

## 线程标识 (pthread_t结构体)
```c
#include <pthread.h>
int pthread_equal(pthread_t tid1, pthread_t tid2);

// 获取线程自身id
pthread_t pthread_self(void);
```

## 线程创建
```c
#include <pthread.h>
int pthread_create(
  pthread_t *restrict tidp,
  const pthread_attr_r *restrict attr,   // 用于定制不同的线程属性
  void *(*start_rtn)(void *),        // 线程执行入口函数
  void *restrict arg);               // 线程执行入口函数参数
```

## 线程终止
如果进程中的任意线程调用了exit、_Exit或者_exit，那么整个进程就会终止。而单个线程的退出有3种方式

```
线程可以简单的从其启动函数中返回，返回值是线程的退出码。
线程可以被同一个进程中的其他线程取消。
线程调用pthread_exit。

void pthread_exit(void *rval_ptr)
```

## pthread_join

```c
可以通过pthread_join自动把线程置于分离状态，这样资源就可以恢复。如果线程已经处于分离状态，pthread_join调用就会失败。
int pthread_join(pthread_t thread, void **rval_ptr)
rval_ptr可以让其他线程用来拿到该线程的退出状态
```

## pthread_cancel
```c
线程可以取消同一个进程中的其他线程
int pthread_cancel(pthread_t tid);

线程可以安排它退出时需要调用的函数，这样的函数称为线程清理处理程序
void pthread_cleanup_push(void(*rtn)(void *), void *arg);
void pthread_cleanup_pop(int execute);
```

## 线程同步

#### 互斥量(mutex)

#### 避免死锁

#### pthread_mutex_timedlock

#### 读写锁

#### 带有超时的读写锁

#### 条件变量

#### 自旋锁

#### barrier
