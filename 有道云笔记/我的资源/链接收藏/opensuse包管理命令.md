```
zypper是个安装软件的工具，但是感觉没有apt-get那么强大。基本用法还是在这里列一下，方便以后使用。

基本语法：zypper [global opts] + commend + [commend opts] + [commend args]

添加源

zypper ar <url> <aliasname>

帮助

zypper help commend
zypper if commend     #info

列出补丁更新

zypper lp             #list patches

打补丁

zypper patch

搜索

zypper se package   #search

删除

zypper rm package   #remove

安装

zypper in package   #install

更新所有包到最新版本

zypper up           #update

更新源

zypper refresh

升级

zypper dup          #dist-update

清空缓存

zypper clean
```

