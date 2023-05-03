将连续多个空格转换为单个tab有多重实现方式，这里使用linux下最常见的三种方式。

### 1、tr命令

```
cat file | tr -s ' ' '\t' > tab.txt
```

### 2、sed命令

```
sed -i 's/\s\+/\t/g' file
```

### 3、使用awk内建函数gsub

```
awk '{gsub(/[ ]/+,"\t",$0);print $0}' file > tab.txt
```

### 4、替换换行符

上面说的是将空格替换为tab键，然后想按上面那样将换行符`\n`替换为多个`----`。

使用`tr`命令时发现替换后多个连续的`----`会给你自动转换成一个`-`，而且替换后没有换行符了。

使用`sed`命令进行替换也是较为复杂，与一般情况不同：

```bash
sed -i ':label;N;s/\n/--------------------/;b label' prot_seq2.fas
```

具体解释见[sed命令替换换行符](https://my.oschina.net/shelllife/blog/118337)

使用awk可以更简单的替换换行符：

```bash
# 第一种方法是格式化字符串
awk '{printf "%s-----",$0}' prot_id.txt
# 第二种方法是更改行记录分隔符ORS
awk 'BEGIN{ORS="---------"}{print $0}' prot_id.txt
```

很明显的`awk`的两种方法都比前面的`sed`要简单得多。