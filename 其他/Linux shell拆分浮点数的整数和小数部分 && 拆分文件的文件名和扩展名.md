```shell
Linux: shell拆分浮点数的整数和小数部分 && 拆分文件的文件名和扩展名

目地：分别获取一个浮点数的整数和小数部分，或者将一个文件的文件名和扩展名拆分开。

可以考虑使用cut或者awk
cut
qingsong@db2a:/tmp$ num1=3.1415
qingsong@db2a:/tmp$ echo $num1 | cut -d '.' -f1
3
qingsong@db2a:/tmp$ echo $num1 | cut -d '.' -f2
1415

awk
qingsong@db2a:/tmp$ echo $num1 | awk -F '.' '{print $1}'
3
qingsong@db2a:/tmp$ echo $num1 | awk -F '.' '{print $2}'
1415

还有一种办法，更加高效和洁简，借助%操作符可以轻松将名称部分从“名称.扩展名”这种格式中提取出来

qingsong@db2a:/tmp$ echo ${num1%.*}
3
qingsong@db2a:/tmp$ echo ${num1#*.}
1415

${VAR%.*} 的含义:
从$VAR中删除位于 % 右侧的通配符（在前例中是.*）所匹配的字符串。通配符从右向左进行匹配。
${VAR%%.*} 的含义:
从$VAR中删除位于 % 右侧的通配符（在前例中是.*）所匹配的字符串。通配符从右向左进行贪婪匹配（尽可能多的匹配）。
${VAR#*.} 的含义:
从$VAR中删除位于 # 右侧的通配符（在前例中是*.）所匹配的字符串。通配符从左向右进行匹配
${VAR##*.} 的含义:
从$VAR中删除位于 # 右侧的通配符（在前例中是*.）所匹配的字符串。通配符从左向右进行贪婪匹配（尽可能多的匹配）。

示例：
qingsong@db2a:/tmp$ url=www.ibm.com
qingsong@db2a:/tmp$ echo ${url%.*}
www.ibm
qingsong@db2a:/tmp$ echo ${url%%.*}
www
qingsong@db2a:/tmp$ echo ${url#*.}
ibm.com
qingsong@db2a:/tmp$ echo ${url##*.}
com

即然能很容易地拆分了，就容易写一个脚本，来批量重命名文件(不考虑文件名有空格的情形)。下面的脚本可以批量重命名所有的 jpg、png文件

#!/bin/bash
#filename: batchrename.sh
#Rename jpg and png files

count=1

# maxdepth参数尽量靠前是一个很好的习惯，可以避免find深度查找
for oldName in `find /tmp  -maxdepth 1 -iname '*.png' -o -iname '*.jpg' -type f`
do
	newName=image-$count.${oldName##*.}
	mv $oldName $newName
	let count++
done
运行效果如下：
qingsong@db2a:/tmp$ find /tmp  -maxdepth 1 -iname '*.png' -o -iname '*.jpg' -type f
/tmp/MyBG1a.jpg
/tmp/V1uBkW4.jpg
/tmp/XXxxzyFr.png
/tmp/A8hB3An8ey.png
/tmp/XXxxeb5t.PNG
qingsong@db2a:/tmp$ bash rename.sh
qingsong@db2a:/tmp$ find /tmp  -maxdepth 1 -iname '*.png' -o -iname '*.jpg' -type f
/tmp/image-5.PNG
/tmp/image-1.jpg
/tmp/image-2.jpg
/tmp/image-4.png
/tmp/image-3.png
————————————————
版权声明：本文为CSDN博主「匿_名_用_户」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qingsong3333/article/details/77600987
```

