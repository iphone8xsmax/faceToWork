### Socket

#### I/O 模型

通常用户进程中的一次**完整 IO 交互**流程分为**两个阶段**。首先是经过**内核空间**，也就是由操作系统处理；紧接着到**用户空间**，由应用程序处理。

必须通过系统调用请求 Kernel 协助完成 IO 操作。

一个网络**输入操作**主要分为两个阶段：

- **等待数据**：等待网络数据到达**网卡**，然后将**数据读取到内核缓冲区**。
- **复制数据**：从内核缓冲区**复制数据**，拷贝到用户空间的应用程序中。

对于一个**套接字**上的输入操作，第一步通常涉及等待数据从网络中到达。当所等待数据到达时，它被复制到内核中的某个**缓冲区**。第二步就是把数据从内核缓冲区复制到应用进程缓冲区。

Unix 有**五种** I/O 模型：

- **阻塞式 I/O**
- **非阻塞式 I/O**
- **I/O 复用（select 和 poll）**
- **信号驱动式 I/O（SIGIO）**
- **异步 I/O（AIO）**

##### 1. 阻塞式 I/O

应用进程被**阻塞**，直到数据从**内核缓冲区**复制到应用**进程缓冲区中**才返回。

应该注意到，在阻塞的过程中，其它应用进程还可以执行，因此阻塞不意味着整个操作系统都被阻塞。因为其它应用进程还可以执行，所以不消耗 CPU 时间，这种模型的 CPU **利用率效率**会比较高。例如 Java BIO。

下图中，**recvfrom()** 用于接收 Socket 传来的数据，并复制到应用进程的缓冲区 buf 中。这里把 recvfrom() 当成系统调用。

```c
ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags, struct sockaddr *src_addr, socklen_t *addrlen);
```

<img src="assets/1563596580857.png" alt="1563596580857" style="zoom:80%;" />

##### 2. 非阻塞式I/O

应用进程执行系统调用 **recvfrom**() 之后，内核返回一个**错误码**。应用进程可以继续执行，但是需要不断的执行系统调用来获知 I/O 是否完成，这种方式称为**轮询**（polling）。

由于 CPU 要处理更多的**系统调用**，因此这种模型的 CPU 利用率比较**低**。

<img src="assets/1563596593363.png" alt="1563596593363" style="zoom:80%;" />

##### 3. 多路复用I/O★

使用 **select() 或者 poll()** 等待数据，并且可以**等待多个套接字**中的任何一个变为**可读**。这一过程会被**阻塞**，当某一个套接字可读时返回，之后再使用 **recvfrom** 把数据从内核**复制**到进程中，所以这里使用了**两个**系统调用。

它可以让**单个进程**具有处理**多个 I/O ==事件==**的能力。又被称为 Event Driven I/O，即==**事件驱动 I/O**==。

多个进程的 IO 可以注册到一个 **Selector** 上，Selector 会对所有的 IO 进行监听。

如果一个 Web 服务器没有 I/O 复用，那么**每一个 Socket 连接**都需要创建一个**线程**去处理。如果同时有几万个连接，那么就需要创建相同数量的线程。相比于多进程和多线程技术，**I/O 复用不需要进程线程创建和切换的开销**，系统开销更小。

<img src="assets/1563596605006.png" alt="1563596605006" style="zoom:80%;" />

IO 复用的**优势在于能够处理更多的连接**，对单个连接的处理不一定比阻塞 IO 快。

对于每一个 Socket，一般都是设置为非阻塞，但是**整个用户的进程其实是一直被阻塞**的，只不过进程是被 select 调用阻塞，而不是被 Socket IO 阻塞。

典型应用有 Java NIO、Nginx。

##### 4. 信号驱动I/O

应用进程使用 **sigaction** 系统调用，内核**立即**返回，应用进程可以继续执行，也就是说等待数据阶段应用进程是**非阻塞**的。内核在数据到达时向应用进程发送 **SIGIO** 信号，应用进程收到之后在信号处理程序中调用 **recvfrom** 调用将数据从内核复制到应用进程中。

相比于非阻塞式 I/O 的轮询方式，信号驱动 I/O 的 CPU 利用率更高。等待数据的时候不是阻塞的，但是**拷贝**数据到应用进程的时候**依然是阻塞**的。

其实用的不太多。

<img src="assets/1563596617974.png" alt="1563596617974" style="zoom:80%;" />

##### 5. 异步I/O

应用进程执行 **aio_read** 系统调用会立即返回，应用进程可以继续执行，不会被阻塞，内核会在所有操作**完成**之后向应用进程**发送信号**。

异步 I/O 与信号驱动 I/O 的区别在于，异步 I/O 的信号是通知应用进程 **I/O 完成**，而信号驱动 I/O 的信号是通知应用进程**可以开始 I/O**，也就是之后的 IO 操作依然需要用户进程阻塞拷贝数据。

<img src="assets/1563596629511.png" alt="1563596629511" style="zoom:80%;" />

真正的实现了异步，是五种 IO 模型中**唯一的异步模型**。

典型应用：**Java7 AIO**，高性能服务器应用。

##### 6. 五大I/O模型比较

###### ① 同步与异步

**同步 IO ** 调用一旦开始，调用者必须等到方法调用返回后，才能继续后续的行为。

**异步 IO** 调用更像一个消息传递，一旦开始，方法调用就会立即返回，调用者就可以继续后续的操作。而，异步方法通常会在另外一个线程中，“真实”地执行着。整个过程，不会阻碍调用者的工作。

###### ② 阻塞与非阻塞

阻塞调用是指调用结果返回之前，当前线程会被挂起。函数只有在得到结果之后才会返回。非阻塞和阻塞的概念相对应，指在不能立刻得到结果之前，该函数不会阻塞当前线程，而会立刻返回。

> **举个生活例子**

如果你想吃一份宫保鸡丁盖饭：

- **同步阻塞**：你到饭馆点餐，然后在那等着，还要一边喊：好了没啊！
- **同步非阻塞**：在饭馆点完餐，就去遛狗了。不过每过十分钟就打电话问饭店：好了没啊！
- **异步阻塞**：遛狗的时候，接到饭馆电话，说饭做好了，让您亲自去拿。
- **异步非阻塞**：饭馆打电话说，我们知道您的位置，一会给你送过来，安心遛狗就可以了。

**阻塞式 I/O、非阻塞式 I/O、I/O 复用和信号驱动 I/O 都是==同步 I/O==**，它们的主要区别在**第一个阶段**，因为**第二阶段**都是**阻塞调用 recvfrom 将数据从内核复制**到用户进程。

非阻塞式 I/O 、信号驱动 I/O 和异步 I/O 在第一阶段不会阻塞。

<img src="assets/1563596642259.png" alt="1563596642259" style="zoom:80%;" />



#### I/O复用★

**==select/poll/epoll==** 都是 **I/O 多路复用**的具体**实现**，select 出现的最早，之后是 poll，再是 epoll。

##### 1. select

```c
int select(int n, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

有三种类型的描述符类型：**readset、writeset、exceptset**，分别对应读、写、异常条件的描述符集合。fd_set 使用数组实现，数组大小使用 FD_SETSIZE 定义。

**timeout** 为超时参数，调用 select 会一直阻塞直到有描述符的事件到达或者等待的时间超过 timeout。

成功调用返回结果大于 0，出错返回结果为 -1，超时返回结果为 0。

```c
fd_set fd_in, fd_out;
struct timeval tv;

// Reset the sets
FD_ZERO( &fd_in );
FD_ZERO( &fd_out );

// Monitor sock1 for input events
FD_SET( sock1, &fd_in );

// Monitor sock2 for output events
FD_SET( sock2, &fd_out );

// Find out which socket has the largest numeric value as select requires it
int largest_sock = sock1 > sock2 ? sock1 : sock2;

// Wait up to 10 seconds
tv.tv_sec = 10;
tv.tv_usec = 0;

// Call the select
int ret = select( largest_sock + 1, &fd_in, &fd_out, NULL, &tv );

// Check if select actually succeed
if ( ret == -1 )
    // report error and abort
else if ( ret == 0 )
    // timeout; no event detected
else
{
    if ( FD_ISSET( sock1, &fd_in ) )
        // input event on sock1

    if ( FD_ISSET( sock2, &fd_out ) )
        // output event on sock2
}
```

##### 2. poll

```c
int poll(struct pollfd *fds, unsigned int nfds, int timeout);
```

pollfd 使用**链表实现**。

```c
// The structure for two events
struct pollfd fds[2];

// Monitor sock1 for input
fds[0].fd = sock1;
fds[0].events = POLLIN;

// Monitor sock2 for output
fds[1].fd = sock2;
fds[1].events = POLLOUT;

// Wait 10 seconds
int ret = poll( &fds, 2, 10000 );
// Check if poll actually succeed
if ( ret == -1 )
    // report error and abort
else if ( ret == 0 )
    // timeout; no event detected
else
{
    // If we detect the event, zero it out so we can reuse the structure
    if ( fds[0].revents & POLLIN )
        fds[0].revents = 0;
        // input event on sock1

    if ( fds[1].revents & POLLOUT )
        fds[1].revents = 0;
        // output event on sock2
}
```

##### 3. select与poll比较

###### ① 功能

select 和 poll 的功能基本相同，不过在一些实现细节上有所不同。

- select 会**修改描述符**，而 poll 不会；
- **select** 的**描述符类型**使用**数组实现**，FD_SETSIZE 大小默认为 **1024**，因此默认只能监听 1024 个描述符。如果要监听更多描述符的话，需要修改 FD_SETSIZE 之后**重新编译**；而 **poll** 的**描述符类型**使用**链表实现**，没有描述符数量的限制；
- poll 提供了更多的**事件类型**，并且对描述符的**重复利用**上比 select 高。
- 如果一个线程对某个描述符调用了 select 或者 poll，另一个线程关闭了该描述符，会导致调用结果不确定。

###### ② 速度

select 和 poll 速度**都比较慢**。

- select 和 poll **每次调用**都需要将**全部描述符**从应用进程缓冲区复制到**内核**缓冲区。
- select 和 poll 的返回结果中没有声明哪些描述符已经准备好，所以如果返回值大于 0 时，应用进程都需要使用轮询的方式来找到 I/O 完成的描述符。

###### ③ 可移植性

几乎**所有的**系统都**支持 select**，但是只有**比较新的**系统支持 **poll**。

##### 4. epoll

```c
int epoll_create(int size);
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)；
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout);
```

epoll_ctl() 用于向内核注册**新的**描述符或者是改变某个文件描述符的**状态**。已注册的描述符在内核中会被维护在一棵**红黑树**上，通过**回调函数**内核会将 I/O 准备好的**描述符**加入到一个**链表**中管理，进程调用 **epoll_wait**() 便可以得到**事件完成**的描述符。

从上面的描述可以看出，**epoll 只需要将描述符从进程缓冲区向内核缓冲区拷贝一次**，并且进程**不需要通过轮询**来获得事件**完成的描述符**。

**epoll 仅适用于 ==Linux== OS。**

epoll 比 select 和 poll 更加**灵活**而且**没有描述符数量限制**。

epoll 对多线程编程更有友好，一个线程调用了 epoll_wait() 另一个线程关闭了同一个描述符也不会产生像 select 和 poll 的不确定情况。

```c
// Create the epoll descriptor. Only one is needed per app, and is used to monitor all sockets.
// The function argument is ignored (it was not before, but now it is), so put your favorite number here
int pollingfd = epoll_create( 0xCAFE );

if ( pollingfd < 0 )
 // report error

// Initialize the epoll structure in case more members are added in future
struct epoll_event ev = { 0 };

// Associate the connection class instance with the event. You can associate anything
// you want, epoll does not use this information. We store a connection class pointer, pConnection1
ev.data.ptr = pConnection1;

// Monitor for input, and do not automatically rearm the descriptor after the event
ev.events = EPOLLIN | EPOLLONESHOT;
// Add the descriptor into the monitoring list. We can do it even if another thread is
// waiting in epoll_wait - the descriptor will be properly added
if ( epoll_ctl( epollfd, EPOLL_CTL_ADD, pConnection1->getSocket(), &ev ) != 0 )
    // report error

// Wait for up to 20 events (assuming we have added maybe 200 sockets before that it may happen)
struct epoll_event pevents[ 20 ];

// Wait for 10 seconds, and retrieve less than 20 epoll_event and store them into epoll_event array
int ready = epoll_wait( pollingfd, pevents, 20, 10000 );
// Check if epoll actually succeed
if ( ret == -1 )
    // report error and abort
else if ( ret == 0 )
    // timeout; no event detected
else
{
    // Check if any events detected
    for ( int i = 0; i < ret; i++ )
    {
        if ( pevents[i].events & EPOLLIN )
        {
            // Get back our connection pointer
            Connection * c = (Connection*) pevents[i].data.ptr;
            c->handleReadEvent();
         }
    }
}
```

##### 5. epoll工作模式

epoll 的描述符事件有两种触发模式：**LT**（level trigger）和 **ET**（edge trigger）。

###### ① LT模式

当 epoll_wait() 检测到**描述符事件到达**时，将此**事件通知进程**，进程可以**不立即**处理该事件，下次调用 epoll_wait() 会再次通知进程。是默认的一种模式，并且同时支持 Blocking 和 No-Blocking。

###### ② ET模式

和 LT 模式不同的是，通知之后进程**必须立即处理事件**，下次再调用 epoll_wait() 时不会再得到事件到达的通知。

很大程度上减少了 epoll 事件被重复触发的次数，因此效率要比 LT 模式高。只支持 No-Blocking，以避免由于一个文件句柄的阻塞读/阻塞写操作把处理多个文件描述符的任务饿死。

##### 6. 应用场景

很容易产生一种错觉认为只要用 epoll 就可以了，select 和 poll 都已经过时了，其实它们都有各自的使用场景。

###### ① select应用场景

select 的 timeout 参数精度为 1ns，而 poll 和 epoll 为 1ms，因此 **select 更加适用于实时性要求比较高**的场景，比如核反应堆的控制。

**select 可移植性更好**，几乎被所有主流平台所支持。

###### ② poll应用场景

poll **没有最大描述符数量的限制**，如果**平台支持**并且对实时性要求不高，应该使用 **poll** 而非 select。

###### ③ epoll应用场景

只需要运行在 **Linux** 平台上，有**大量的描述符**需要同时轮询，并且这些连接最好是**长连接**。

需要同时监控**小于 1000** 个描述符，就**没有必要**使用 epoll，因为这个应用场景下并不能体现 epoll 的优势。

需要监控的描述符状态**变化多**，而且都是非常短暂的，也**没有必要**使用 epoll。因为 epoll 中的所有描述符都存储在内核中，造成每次需要对描述符的状态改变都需要通过 epoll_ctl() 进行系统调用，频繁系统调用降低效率。并且 epoll 的描述符存储在内核，不容易调试。





#### 参考资料

- Stevens W R, Fenner B, Rudoff A M. UNIX network programming[M]. Addison-Wesley Professional, 2004.
- [Boost application performance using asynchronous I/O](https://www.ibm.com/developerworks/linux/library/l-async/)
- [Synchronous and Asynchronous I/O](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365683(v=vs.85).aspx)
- [Linux IO 模式及 select、poll、epoll 详解](https://segmentfault.com/a/1190000003063859)
- [poll vs select vs event-based](https://daniel.haxx.se/docs/poll-vs-select.html)
- [select / poll / epoll: practical difference for system architects](http://www.ulduzsoft.com/2014/01/select-poll-epoll-practical-difference-for-system-architects/)
- [Browse the source code of userspace/glibc/sysdeps/unix/sysv/linux/ online](https://code.woboq.org/userspace/glibc/sysdeps/unix/sysv/linux/)