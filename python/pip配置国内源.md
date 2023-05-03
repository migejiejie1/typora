#### pip使用国内源

```shell
# 下面是收集的一些国内的pip源：
阿里云 http://mirrors.aliyun.com/pypi/simple/
中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/ 
豆瓣(douban) http://pypi.douban.com/simple/ 
清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/

# 临时修改
pip install psutil -i http://mirrors.aliyun.com/pypi/simple --trusted-host mirrors.aliyun.com

# 永久修改pip源
vim ~/.pip/pip.conf
[global]
index-url=http://mirrors.aliyun.com/pypi/simple # 指定下载源

[install]
trusted-host=mirrors.aliyun.com # 指定域名

# 查看镜像地址：
$ pip3 config list   
global.index-url='http://mirrors.aliyun.com/pypi/simple'
install.trusted-host='mirrors.aliyun.com'
```

