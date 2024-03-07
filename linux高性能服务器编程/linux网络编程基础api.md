### 5.8.3通用数据读写函数 
```c
msghdr
    void * msg_name;// 指向socket 结构地址 tcp 设置 未 null ,连接的时候已经存在该信息
    socklent_t msg_namelen; socket 地址长度
    struct iovec *msg_iov;
    int msg_iovlen;// iovec 存在多少个  sendmsg 使用 会将  msg_iovlen 个 msg_iov 里面的数据一并发送

    // 下面两个用于辅助数据的传递
    void *msg_control;
    socklen_t * msg_controllen;


    int msg_flags; 无需设定 会自动将 recvmsg  sendmsg 的 flags 赋值 上去 ,也会将某些执行过程中更新后的操作设置到 msg_flags 


iovec
    void * iov_base; 内存起始地址
    size_t iov_len; 内存长度
```

##  5.9 带外标志
```c
在实际过程中 我们无法判断 外带数据(OOB)何时到来

kernel 检查到TCP 紧急标志将会有通知应用程序接收
    两种常见的方式通知
    1.IO复用会产生异常事件
    2.SIGURG 信号


    应用程序手动紧急标志的信号 需要知道数据流的具体位置用于接收数据
    #include<sys/socket.h>
    int sockatmark(int sockfg);

    sockatmark 判断 sockfg 是否带有外带标记
    sockatmark 返回 1
        我们利用MSG_OOB 标志  recv 调用外带数据
        返回0 则 没有紧急标志处理
```

## 5.10 地址信息函数
