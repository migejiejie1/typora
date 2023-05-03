**需求描述**:

　　linux环境中,在使用date命令的时候,可以通过-d指定日期的字符串来显示日期

**操作过程**:

1.通过date显示昨天的日期

```shell
[root@redhat6 ~]# date -d 'yesterday'                       #通过-d后面接日期上字符串yesterdate
Tue Jul  3 15:36:06 CST 2018
[root@redhat6 ~]# date -d 'yesterday' '+%Y-%m-%d %H:%M:%S'  #接上日期字符串,显示具体的日期,然后通过+转换为具体的格式
2018-07-03 15:36:08
```



2.通过-d接上具体日期字符串

```shell
[root@redhat6 ~]# date -d '2008-09-18 20:00:00'                       #主要是显示某个特定的日期,以默认的格式显示
Thu Sep 18 20:00:00 CST 2008
[root@redhat6 ~]# date -d '2008-09-18 20:00:00' '+%Y-%m-%d %H:%M:%S'  #以特定格式显示某个日期
2008-09-18 20:00:00
```

 

3.查看3天之后的日期

```shell
[root@redhat6 ~]# date -d '+3 days' '+%Y-%m-%d %H:%M:%S'
2018-07-07 15:44:44
```

 

4.查看3天之前的日期

```shell
[root@redhat6 ~]# date -d '-3 days' '+%Y-%m-%d %H:%M:%S'
2018-07-01 15:45:10
```

 

5.进行天,小时,分钟的计算

```shell
[root@redhat6 ~]# date -d '-3 days 2 hours' '+%Y-%m-%d %H:%M:%S'
2018-07-01 17:45:36
[root@redhat6 ~]# date -d '-3 days -2 hours' '+%Y-%m-%d %H:%M:%S'
2018-07-01 13:45:42
[root@redhat6 ~]# date -d '+3 days +2 hours 1 minute' '+%Y-%m-%d %H:%M:%S'
2018-07-07 17:46:56
[root@redhat6 ~]# date -d '+3 days +2 hours -1 minute' '+%Y-%m-%d %H:%M:%S'
2018-07-07 17:45:0
```



**小结:**最好的方式就是通过正负号的方式进行日期的向前和向后.