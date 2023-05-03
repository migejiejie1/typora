```shell
排除Nfc目录
find . -path ./Nfc -prune -o -type f -name *.mk -print

排除多个目录
find . \( -path ./Gallery2 -o -path ./HTMLViewer -o -path ./Nfc \) -prune -o  -name *.mk -print

-o 或运算, 类型的还有 -a:与(可省略,默认就是与); -not:非(和!意义相同)
-type 要搜索的文件类型, f:普通文件; d:目录; l:符号链接; s:Socket; 其它参见man手册
( expr ) 括号用于把多个表达式括起来, 但要注意在shell中要以 \( 表示, 且()与expr之间也要留空格

统计目录结构
find ./ -maxdepth 2 -mindepth 1 -type d  
```

