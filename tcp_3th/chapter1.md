```
事务:c 向 s 发送请求 ，s 向 c 做出应答

udp cs 事务的最短时间 rtt(网络往返时间)+spT(服务器处理用户请求时间)

server:
    socket
    bind
    recvform ---->当前线程sleep


client:
    socket
    sendto
    recvform 当前线程sleep


server:
    wakeup 接受到客户端数据
    服务端处理接受到的数据
    sendto
    recvform 当前线程sleep

client:
    wakeup 接受到服务器的数据


根据Internet 路劲研究 30% 的肯能 请求和响应的数据经过的站点是不一样的

udp 不可靠协议 数据可能丢失 可能失序或重复,实用应用程序必须处理这些问题

    recvfrom 设置定时器
            检测数据丢失 并重传请求
    客户端测量RTT 并动态更新

    服务器 将每个客户端最后一次请求的响应暂存起来,必要时重传这个应答即可,而不需要再次处理这个请求

    client [uniqueid+data]  server
    server [uniqueid + rev data] client  客户端通过 uniqueid 可以让请求和 响应数据对应起来
```

```
client --- >server
connect 启动tcp 的三次握手
write 发送请求数据
shutdown 关闭连接的一半  客户端向服务端发送一个文件结束通知 FIN
    客户端此时仍然能读取数据,但是不能发送数据   (TCP 半关闭)

```

```
tcp 的time_wait 状态


client shutdown --->fin --->server
    client 半关闭

当 client 和 服务端都完全关闭之后仍然需要保持time_wait 直至两倍最大生存时间(2msl)
一般建议msl 值是120s ,也就是说处于time_wait 状态会保持4分钟

time_wait 状态时候,同一连接 客户端 + 端口号不能重复打开



client shutdwon  发送 fin client 主动关闭 ,因而 time_wait 出现在客户端

客户端 time_wait 状态 ，tcp 会为这个关闭的连接保留一定的状态信息,用来处理网络延迟一段时间，
在关闭之后到达短报文


当服务器close 之后 fin 数据丢失  fin 会重新传输 ,即使现在客户端的状态time_wait 也会获取
到fin 数据 之后重新向服务返回 ack 



```
