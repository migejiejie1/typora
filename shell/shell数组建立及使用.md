**shell数组建立及使用**

#### 一：定义

- 变量：存储单个元素的内存空间
- 数组：存储多个元素的连续的内存空间，相当于多个变量的集合
- 数组名和索引
  - 索引：编号从0 开始，属于数值索引
     注意：索引可支持使用自定义的格式，而不仅是数值格式，即为关联索引， bash4.0 版本之后开始支持 bash 的数组支持稀疏格式（索引不连续）

#### 二：数组的声明与定义

declare -a ARRAY_NAME
 declare -A ARRAY_NAME: 关联数组
 注意：两者不可相互转换

```ruby
[root@centos7 ~]# a=(1 2 3 4 5)
[root@centos7 ~]# echo $a
```

> 一对括号表示的是数组，数组元素用空格符号分隔开

#### 三：数组的读取与赋值

- 得到长度

```ruby
[root@centos7 ~]# echo ${#a[@]}5
```

> 用${#数组名[ @或* ]}可以得到数组长度

- 读取

```ruby
  [root@centos7 ~]# echo ${a[2]}3
 [root@centos7 ~]# echo ${a[*]}1 2 3 4 5
```

> 用${ 数组名[ 下标 ] }，下标是从0开始 。下标是：*或者@得到整个数组内容

- 赋值

```ruby
[root@centos7 ~]# a[1]=100
[root@centos7 ~]# echo ${a[*]}
1 100 3 4 5
[root@centos7 ~]# a[5]=100
[root@centos7 ~]# echo ${a[*]}
1 100 3 4 5 100
```

> 直接通过 数组名[ 下标 ] 就可以对其直接进行引用赋值，如果下标不存在，自动添加一个新的数组元素

- 删除

```ruby
[root@centos7 ~]# a=(1 2 3 4 5)
[root@centos7 ~]# echo ${a[*]}
1 2 3 4 5
[root@centos7 ~]# unset a
[root@centos7 ~]# echo ${a[*]}
[root@centos7 ~]# a=(1 2 3 4 5)
[root@centos7 ~]# unset a[1]
[root@centos7 ~]# echo ${a[*]}
1 3 4 5
[root@centos7 ~]# echo ${#a[*]}4
```

> 直接通过：unset 数组[ 下标 ] 可以清除相应的元素，不带下标，清除整个数组

#### 四：特殊使用

- 分片

```ruby
[root@centos7 ~]# a=(1 2 3 4 5)
[root@centos7 ~]# echo ${a[@]:0:3}
1 2 3
[root@centos7 ~]# echo ${a[@]:1:4}
2 3 4 5
[root@centos7 ~]# c=(${a[@]:1:4})
[root@centos7 ~]# echo ${#c[@]}
4
[root@centos7 ~]# echo ${c[*]}
2 3 4 5
```

> 直接通过 ${ 数组名 [ @或*]: 起始位置: 长度 } 切片原先的数组，返回时字符串，中间用空格分开，因此如果加上”( )”，将得到切片数组。上面的例子中，c 就是一个新的数组

- 替换

```ruby
[root@centos7 ~]# a=(1 2 3 4 5)
[root@centos7 ~]# echo ${a[@]/3/100}
1 2 100 4 5
[root@centos7 ~]# echo ${a[@]}
1 2 3 4 5
[root@centos7 ~]# a=(${a[@]/3/100})
[root@centos7 ~]# echo ${a[@]}
1 2 100 4 5
```

> 调用方法是：${ a[@或*] /查找字符/替换字符 }，该操作不会改变原来的数组内容，如果需要修改，可以参考上面的例子，重新定义数组

从上面讲到的，大家可以发现Linux shell的数组已经很强大了。