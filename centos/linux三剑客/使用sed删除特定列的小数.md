### 如何解决使用sed删除特定列的小数？

https://www.jb51.cc/faq/1819978.html

我正在处理一个文件, 我想用特定列的小数截断数字。 其中三行是：

```shell
123;rr;2;RRyO,chess mobil;pio;**25.766**;1;0;24353;21.876;;S

1243;rho;9;RpO,chess yext cat;downpio;**67.98**;1;0;237753;25.346;;S

1243;rho;9;RpO,chess yext cat;pio;**73**;1;0;237753;25.346;;S
```

我想要这个[输出](https://www.jb51.cc/tag/shuchu/)：

```shell
123;rr;2;RRyO,chess mobil;pio;**25**;1;0;24353;21.876;;S

1243;rho;9;RpO,chess yext cat;downpio;**67**;1;0;237753;25.346;;S

1243;rho;9;RpO,chess yext cat;pio;**73**;1;0;237753;25.346;;S
```

我已经尝试过这些[代码](https://www.jb51.cc/tag/daima/)：

```
sed  -e '/^.\+pio$/,/^\..\*;[[:digit:]];[[:digit:]];.\*;.\*;.\*;.\*[[:space:]]$/d' data.csv
```

但是没有用... 请问有什么建议吗？

### 解决方法

使用您显示的示例，请尝试以下操作。您可以通过 `awk` 的 sprintf 函数简单地将浮点数转换为数字。

```
awk 'BEGIN{FS=OFS=";"} {$6=sprintf("%d",$6)} 1' Input_file
```

来自 `awk` 的手册页：

sprintf(fmt,expr-list) 根据fmt打印expr-list，并返回 结果字符串。

,

我还没有完全对你的 sed 命令进行逆向工程，但这似乎有效：

```
sed 's/\(.*pio;[0-9]*\)\.[0-9]*/\1/' data.csv
```

,

你可以使用

```sh
sed 's/^\(\([^;]*;\)\{5\}[0-9]*\)[^;]*/\1/' data.csv
```

*详情*：

```shell
^ - 字符串的开始
\(\([^;]*;\)\{5\}[0-9]*\) - 第 1 组 (\1)：
\([^;]*;\)\{5\} - 除 ; 和 ; 之外的任何零个或多个字符出现五次
[0-9]* - 零个或多个数字
[^;]* - 除 ; 之外的零个或多个字符。
```

见[online demo](https://ideone.com/XZtOey)：

```sh
s='123;rr;2;RRyO,chess mobil;pio;25.766;1;0;24353;21.876;;S1243;rho;9;RpO,chess yext cat;downpio;67.98;1;0;237753;25.346;;S1243;rho;9;RpO,chess yext cat;pio;73;1;0;237753;25.346;;S'sed 's/^\(\([^;]*;\)\{5\}[0-9]*\)[^;]*/\1/' <<< "$s"
```

输出：

```sh
123;rr;2;RRyO,chess mobil;pio;25;1;0;24353;21.876;;S1243;rho;9;RpO,chess yext cat;downpio;67;1;0;237753;25.346;;S1243;rho;9;RpO,chess yext cat;pio;73;1;0;237753;25.346;;S
```

,

这可能对你有用（GNU sed）：

```
sed -E 's/([0-9]+)(\.[0-9]+)?|([^;]+)/\1\3/6' file
```

字段可以是数字、带小数的数字或非数字。

在第六个这样的字段中，只有存在时才返回数字部分。