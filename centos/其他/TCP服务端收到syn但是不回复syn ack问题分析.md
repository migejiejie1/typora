```shell
最近在分析客户的一个问题时遇到了一种奇怪的情况，客户在服务端开启了某个端口，但是在客户端telnet确一直不通。通过在服务端抓包发现，客户端的syn分节已经到达，但是服务端并没有应答。查看了一下当前连接数，发现连接数也不大，所以排除是连接队列满造成的。后来忽然想起了net.ipv4.tcp_tw_recycle选项可能引起这个问题，于是关闭了这个选项，问题果然得以解决。这里分析一下原因。

有些服务器（当然客户端也可以）为了避免TIME_WAIT状态占用连接，希望能加快TIME_WAIT状态的回收，通常将net.ipv4.tcp_tw_recycle选项开启。当然这个选项的生效要依赖net.ipv4.tcp_timestamps选项的开启。虽然开启这个选项能够加快TIME_WAIT连接的回收，但却引入了另一个问题。我们先看下tcp_tw_recycle选项的工作机制：
当开启了tcp_tw_recycle选项后，当连接进入TIME_WAIT状态后，会记录对应远端主机最后到达分节的时间戳。如果同样的主机有新的分节到达，且时间戳小于之前记录的时间戳，即视为无效，相应的数据包会被丢弃（rfc1323）。
    Linux是否启用这种行为取决于tcp_timestamps和tcp_tw_recycle，因为tcp_timestamps缺省就是开启的，所以当tcp_tw_recycle被开启后，实际上这种行为就被激活了。
    现在很多公司都用LVS做负载均衡，通常是前面一台LVS，后面多台后端服务器，这其实就是NAT，当请求到达LVS后，它修改地址数据后便转发给后端服务器，但不会修改时间戳数据，对于后端服务器来说，请求的源地址就是LVS的地址，加上端口会复用，所以从后端服务器的角度看，原本不同客户端的请求经过LVS的转发，就可能会被认为是同一个连接，加之不同客户端的时间可能不一致，所以就会出现时间戳错乱的现象，于是后面的数据包就被丢弃了，具体的表现通常是是客户端明明发送的SYN，但服务端就是不响应ACK，还可以通过下面命令来确认数据包不断被丢弃的现象：

shell> netstat -s | grep timestamp
... packets rejects in established connections because of timestamp
    如果服务器身处NAT环境，安全起见，通常要禁止tcp_tw_recycle，至于TIME_WAIT连接过多的问题，可以通过激活tcp_tw_reuse来缓解（只对客户端有作用）。


当然关闭tcp_timestamps选项也是可以避免这个问题的：
设置sysctl.conf里面tcp_timestamps=0
也可以只用命令sysctl -w net.ipv4.tcp_timestamps=0
    但个人建议关闭tcp_tw_recycle选项，而不是timestamp；因为 在tcp timestamp关闭的条件下，开启tcp_tw_recycle是不起作用的；而tcp timestamp可以独立开启并起作用。此外tcp timestamp还和其他选项起作用有关，如tcp_tw_reuse。
```

