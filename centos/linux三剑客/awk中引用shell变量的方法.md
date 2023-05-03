```shell
awk中引用shell变量的方法
1、通过命令行参数定义变量时引用：

awk -v awk变量名= shell变量名

 #!/bin/bash

var4bash=test

awk -v var4awk="$var4bash"  'BEGIN { print  var4awk}' 

2、在awk中直接引用，使用"'$var'" ，注意使用前格式必须是先用单引号括住再用双引号括住：

#!/bin/bash

var=test

awk 'BEGIN { print "'$var'" }' 

注意：如果var有空格、转义字符等特殊字符，最好在$var外再用一个双引号括住： "'"$var"'"

另外，如果不是用脚本文件方式执行，直接在shell里执行，需要先用export var=test 导出为环境变量，

这样其后的awk子进程才会有该变量。
```

