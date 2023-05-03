```shell
1. git config --global user.name 'mige' 设置全局用户名
2. git config --global user.email  'mige@qq.com' 设置全局邮箱
3. git config --global color.ui true 设置颜色 开启
4. git config --list 配置列表

1.git init 初始化仓库 把一个目录初始化为版本仓库(可以是空的目录也可以是带内容的目录)
2.git status 查看当前仓库的状态
3.git add file 添加文件到暂存区
4.git add . 或者git add * 添加当前所有的文件到暂存区
5.git rm --cached file 撤出暂存区
6.git rm -f file 同时删除工作目录和暂存区的文件
7.git commit -m 'xxx' 从缓存区提交到本地仓库
小结:如何真正意义上通过版本控制系统管理文件
1.工作目录必须有个代码文件
2.通过git add file 添加到暂存区域
3.通过git commit -m "你自己输入的信息” 添加到本地仓库
8.git mv old-filename new-filename 直接更改文件名称更改完直接commit提交即可
9.git diff 默认比对工作目录和暂存区有什么不同
10.git diff --cached 比对暂存区域和本地仓库
11.git commit - am "add newfile" 如果某个文件己经被仓库管理,如果在更改此文件直接需要一条命令提交即可
12.git log 查看历史提交过的信息
-p 查看具体的改动
-1 查看最近一次
--oneline 一行显示
--decorate 显示当前指针位置
13.git reset --hard 295e997 回滚数据到某一个提交
14.git log --oneline --decorate 查看当前指针的指向
15.git reflog 查看所有的历史操作
16.git branch 查看分支
17.git branch testing 创建分支testing
18.git checkout testing 切换分支
19.git checkout -b testing 创建并切换到一个新的分支
20.git branch -d testing 删除分支testing
21.git merge testing 合并分支到master
22.git tag -a v1.0 ceb5f3d -m 'add version 1.0'  创建标签v1.0给以前的位置'ceb5f3d'
23.git tag -a v2.0 -m 'add version 2.0' 给当前指针位置创建标签v2.0
24.git tag  查看标签信息
25.git show v1.0 查看标签的具体信息
26.git tag -d v1.0 删除标签
27.git remote add origin git@github.com:migejiejie/origin 添加远程仓库  名称为origin
28.git remote 查看当前的远程仓库的名称
29.git push -u origin master 推送master分支的代码到远程origin仓库中 (-u == --set-upstream)
30.git clone git@github.com:migejiejie/test.git 克隆代码到本地
31.git remote remove test 删除添加在本体的远程仓库
32.git pull origin master 同步远程仓库
```

