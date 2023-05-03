**python中的问题解决：TypeError: 'encoding' is an invalid keyword argument for this function**

使用open函数时出现：

![image-20221227095841135](../../Image/image-20221227095841135.png)

关键代码：

```python
fp = open('./config_xlsx.txt', encoding= 'gb18030')
```

解决方案一：

改成：

```python
import io
fp = io.open('./config_xlsx.txt', encoding= 'gb18030')
```

但是，打印出来的txt内容有很多转义字符:

![image-20221227095937572](../../Image/image-20221227095937572.png)

解决方案二(终极解决方案)：

先查看python版本，python --version，发现是2.7

果断升级到3.4

不需要import io，也不会有转义字符！！！