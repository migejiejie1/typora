**Shell-Sed 单引号、双引号同时替换**

```shell
单引号、双引号同时替换为双引号为例

方法一：
sed -i "s/\\\"\\\'/\\\"/g"  test.txt

方法二：
sed -i  s#\'\"#\"#g test.txt
```

