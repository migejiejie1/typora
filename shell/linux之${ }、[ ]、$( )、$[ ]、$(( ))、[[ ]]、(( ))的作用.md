**linux之${ }、[ ]、$( )、$[ ]、$(( ))、[[ ]]、(( ))的作用**

#### linux常用符号

#### 1、\${ }与\$

#### 2、[ ]与test

#### 3、$()和``

#### 4、\$[ ]和\$(())

#### 5、[[ ]]

#### 6、(( ))



### 1、${ } 与 $

$与${ }都是用来引用变量的，只不过${ }可以指定变量边界，也可用于对字符串变量进行截取等处理，具体用法可参考如下blog
**[linux之${}符号详解](https://blog.csdn.net/qq_43382735/article/details/121606145?login=from_csdn)**

### 2、[ ]与test

[ ]是test命令的另一种形式，用于判断某个表达式的返回值是0或者非0，常用于if命令的判断条件
`test $a == "linux"` 等于 `[ $a == "linux" ]`

```shell
if test $a == "linux"
then
	echo "i am linux"
elif [ $a == "java" ] 
then
	echo "i am java"
fi
```

> 注意 [ 后和 ] 前都需要有空格，并且 == 两边也都要有空格

具体关于test和[ ] 的用法可以参考:

**[linux之test命令详解](https://blog.csdn.net/qq_43382735/article/details/120986363?login=from_csdn)**

### 3、$() 和 ``

$()和``的作用一致，都是用来做命令替换用，一般用于将命令返回的结果传递给变量

```shell
a=$(ls /home/hadoop101/)
a=`ls /home/hadoop101/\`
a保存的是上述命令的返回值，即一个目录列表
```

### 4、$[ ] 和 $(( ))

$[]和$(())的作用一样，都是进行数学运算的，支持 +-*/% ,并且在$[ ]和$(( ))中使用变量不需要$引用，可以直接使用变量名

```shell
echo $[2+7]
9
a=3;b=4;echo $[$a+$b]
7
echo $((2+7))
9
a=3;b=4;echo $(($a+$b))
7
a=3;b=4;echo $((a+b))
7
```

同样可以进行数学运算的还有expr命令和bc命令

```shell
echo `expr 3 + 4`
7
echo `expr 3+4`
3+4
echo `expr 3 \* 4`
12
注意：+-*/的左右各需要一个空格，expr 3+4 则无法正确运算，另外使用*/需要转义字符，加减不需要
```

bc是linux的计算器，是交互式命令，但是bc支持从标准输入中读取参数及逆行运算，但是bc不支持从命令行中读取运算式

```shell
bc "3+4"
File 3+4 is unavailable.
echo "3+4"|bc
7
```

### 5、[[ ]]

[[ ]]是[ ]的增强版，其返回值也是0或者非0

- **在[[ ]]中使用> < 等符号不需要转义字**

```shell
[root@linuxforliuhj ~]# cat a.txt
if [ $1 \> 5 ]
then
        echo "$1的值大于5"
else
        echo "$1的值小于5"
fi
```

如果使用[[ ]] 的话则可以去掉转义字符

```bash
if [[ $1 > 5 ]]
then
        echo "$1的值大于5"
else
        echo "$1的值小于5"
fi
```

也可以使用&&或者||,但是只支持==或者!=的连接

```lua
if [[ $a != 3 && $a != 10 ]] 
then
	echo "hello i am linux"
fi
```

如下的使用方法是错误的,因为&&不支持>的连接判断，只支持==和!=的连接判断

```lua
错误案例：
if [[ $a > 3 && $a != 10 ]] 
then
	echo "hello i am linux"
fi
```

如果不使用[[ ]]的话则需要这样写：

```lua
if [[ $a != 3 -a $a != 10 ]] 
then
	echo "hello i am linux"
fi
或者
if [[ $a != 3 ] && [ $a != 10 ]] 
then
	echo "hello i am linux"
fi
```

**[[ ]]在比较字符串支持正则匹配和通配符匹配**

在[[ ]]中进行 **==** 或者 **!=** 比较时可以进行通配符匹配

```lua
案例1：
if [[ linux == l?nu? ]]
then
	echo "i am linux"
else
	echo "i am not linux"
fi

案例2：
if [[ linux == li* ]]
then
	echo "i am linux"
else
	echo "i am not linux"
fi
```

在[[ ]]中可以使用 **=~** 进行正则匹配

```lua
案例1：
if [[ linux =~ ^li ]]
then
	echo "i am linux"
else
	echo "i am not linux"
fi

案例2：
if [[ linux =~ ^li[abn]ux ]]
then
	echo "i am linux"
else
	echo "i am not linux"
fi
```

### 6、(( ))

(( )）的主要用法大概有三个：

- 与$结合使用进行数学运算$(( ))
- 在for循环命令中控制循环，类似于c语言
- 改变变量的值，且不需要$引用

```bash
for((i=1;i<10;i++))
do
        echo "this is $i"
done
```

```shell
i=0
while [ $i -le 10 ]
do
        echo "this is $i"
        ((i++))
done

或者

i=0
while [ $i -le 10 ]
do
        echo "this is $i"
        ((i=i+1))
done
```

