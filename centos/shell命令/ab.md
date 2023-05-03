```shell
ab
相关命令：webbench
ab是专门用于HTTP Server的benchmark testing，可以同时模拟多个并发请求。ab的设计意图是描绘当前所安装的Apache的执行性能,主要是显示所安装的Apache每秒可以处理多少个请求

用法： ab [options] [http[s]://]hostname[:port]/path

参数:

-A auth-username:password
    对服务器提供BASIC认证信任。 用户名和密码由一个:隔开，并以base64编码形式发送。 无论服务器是否需要(即, 是否发送了401认证需求代码)，此字符串都会被发送。

-c concurrency
    一次产生的请求个数。默认是一次一个。

-C cookie-name=value
    对请求附加一个Cookie:行。 其典型形式是name=value的一个参数对。 此参数可以重复。

-d 显示"percentage served within XX [ms] table"的消息(为以前的版本提供支持)。

-e csv-file
    产生一个以逗号分隔的(CSV)文件， 其中包含了处理每个相应百分比的请求所需要(从1%到100%)的相应百分比的(以微妙为单位)时间。 由于这种格式已经“二进制化”，所以比'gnuplot'格式更有用。

-g gnuplot-file
    把所有测试结果写入一个'gnuplot'或者TSV (以Tab分隔的)文件。 此文件可以方便地导入到Gnuplot, IDL, Mathematica, Igor甚至Excel中。 其中的第一行为标题。

-h 显示使用方法。

-H custom-header
    对请求附加额外的头信息。 此参数的典型形式是一个有效的头信息行，其中包含了以冒号分隔的字段和值的对 (如, "Accept-Encoding: zip/zop;8bit").

-i 执行HEAD请求，而不是GET。

-k 启用HTTP KeepAlive功能，即, 在一个HTTP会话中执行多个请求。 默认时，不启用KeepAlive功能.

-n requests 
    在测试会话中所执行的请求个数。 默认时，仅执行一个请求，但通常其结果不具有代表意义。

-p POST-file
    包含了需要POST的数据的文件.

-P proxy-auth-username:password
    对一个中转代理提供BASIC认证信任。 用户名和密码由一个:隔开，并以base64编码形式发送。 无论服务器是否需要(即, 是否发送了401认证需求代码)，此字符串都会被发送。

-q 如果处理的请求数大于150， ab每处理大约10%或者100个请求时，会在stderr输出一个进度计数。 此-q标记可以抑制这些信息。

-s 用于编译中(ab -h会显示相关信息)使用了SSL的受保护的https， 而不是http协议的时候。此功能是实验性的，也是很简陋的。最好不要用。

-S 不显示中值和标准背离值， 而且在均值和中值为标准背离值的1到2倍时，也不显示警告或出错信息。 默认时，会显示 最小值/均值/最大值等数值。(为以前的版本提供支持).

-t timelimit
    测试所进行的最大秒数。其内部隐含值是-n 50000。 它可以使对服务器的测试限制在一个固定的总时间以内。默认时，没有时间限制。

-T content-type
    POST数据所使用的Content-type头信息。

-v verbosity
    设置显示信息的详细程度 - 4或更大值会显示头信息， 3或更大值可以显示响应代码(404, 200等), 2或更大值可以显示警告和其他信息。

-V 显示版本号并退出。

-w 以HTML表的格式输出结果。默认时，它是白色背景的两列宽度的一张表。

-x <table>-attributes
    设置<table>属性的字符串。 此属性被填入<table 这里 >.

-X proxy[:port]
    对请求使用代理服务器。

-y <tr>-attributes
    设置<tr>属性的字符串.

-z <td>-attributes
    设置<td>属性的字符串.

===================================


[root@localhost ~]# ab -n 1000 -c 10 http://localhost/index.php      #对index.php进行，1000次请求，并发用户10的压力测试
This is ApacheBench, Version 2.3 <$Revision: 655654 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
 
Benchmarking localhost (be patient)  #基准本地主机(请耐心)
Completed 100 requests  #完成1000个请求
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests
 
Server Software:        Apache/2.2.19
Server Hostname:        localhost
Server Port:            80
 
Document Path:          /index.php
Document Length:        45 bytes   #文档长度
 
Concurrency Level:      10         #并发级别
Time taken for tests:   0.454 seconds    #测试时间
Complete requests:      1000  #完成要求
Failed requests:        0           #失败的请求
Write errors:           0              #写错误
Total transferred:      322644 bytes    总传输
HTML transferred:       45090 bytes    传输的HTML
Requests per second:    2204.64 [#/sec] (mean)   每秒请求
Time per request:       4.536 [ms] (mean)    每个请求的时间 (平均)
Time per request:       0.454 [ms] (mean, across all concurrent requests)  每个请求的时间 (所有并发请求的平均值)
Transfer rate:          694.64 [Kbytes/sec] received   传输速率:接收到
 
Connection Times (ms)   连接次数
              min  mean[+/-sd] median   max  最小平均值
Connect:        0    1   0.5      1       5  连接
Processing:     1    3   5.6      2      62  处理
Waiting:        0    3   5.6      2      62  等待
Total:          1    4   5.6      3      63   总数
 
Percentage of the requests served within a certain time (ms) 特定时间内服务的请求百分比(ms)
  50%      3
  66%      4
  75%      4
  80%      4
  90%      6
  95%     12
  98%     26
  99%     33 
 100%     63 (longest request)  (最长请求)
  

```

