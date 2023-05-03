```shell
# 初始化git仓库
git init
# 设置当前备份者的身份信息
+ 配置用户名: git config --global user.name "mige"
+ 配置邮箱: git config --global user.email "mige@gmail.com"
# 将代码存储到.git仓库中
1. 把代码放到仓库门口 (暂存区)
    git add ./file_or_dir 将所指定的文件提交至仓库门口(暂存区)
    git add ./ 将所有修改过的文件提交至仓库门口(暂存区)
2. 把仓库门口的代码放到仓库中 (版本库)
    git commit -m '这是对这次添加的东西的说明'
工作区(不包含.git) | 暂存区 | 版本库

3. 可以一次性把我们修改的代码放到仓库中(版本库)
    git commit --all -m '一些说明'
    + --all/-a 表示是把所有修改的文件提交到版本库
# 查看当前的状态
可以用来查看当前代码有没有被放到仓库中去
git status
# git中的忽略文件
- .gitignore 在这个文件中可以设置要被忽略的文件或者目录.
- 被忽略的文件或者目录不会被提交到仓库中
- 在 .gitignore 中可以书写要被忽略的文件的路径, 以/开头,
一行写一个路径, 这些路径所对应的文件都会被忽略, 不会被提交到仓库中
+ 写法
/.idea 会忽略.idea文件
/js 会忽略js目录里的所有文件
/js/*.js 会忽略js目录下所有js文件
# 查看日志
git log 查看历史提交的日志
git log --oneline 可以查看简洁版的日志
# 回退到指定的版本
git reset --hard Head~0 
+ 表示回退到上一次代码提交时的状态
git reset --hard Head~1
+ 表示回退到上上次代码提交时的状态
git reset --hard [版本号]
例: git reset --hard 35e96f6
+ 可以通过版本号精确的回退到某一次提交时的状态
git reflog
+ 可以看到每一次切换版本的记录, 可以看到所有提交的版本号
# 分支
默认是有一个主分支master
#创建分支
git branch dev
+ 创建了一个dev分支
+ 在刚创建时dev分支里的东西和master分支里的东西是一样的
#切换分支
git checkout dev
+ 切换到指定的分支, 这里的切换到名为dev的分支
git branch
+ 可以查看当前有哪些分支
#合并分支
git merge dev
+ 合并分支内容, 把当前分支与指定的分支(dev)进行合并
+ 当前分支指的是 git branch 命令输出的前面有 * 号的分支
合并时如果有冲突, 需要手动去处理, 处理后还需要在提交一次 

# 删除分支
git branch -d dev
+ 将dev分支删除

# Github 
不是git, 只是一个网站
只不过这个网站提供了允许通过git上传代码的功能

# 提交代码到github(当作git服务器来用)
git push [地址] master
+ 示例: git push https://github.com/migejiejie1/test.git master
+ 会把当前分支的内容上传到远程的master分支上
git pull [地址] master
+ 示例: git pull https://github.com/migejiejie1/test.git master
+ 会把远程分支的数据得到(注意本地, 要初始化一个仓库)
git clone [地址]
+ 会得到远程仓库相同的数据, 如果多次执行会覆盖本地内容

# ssh方式上传代码
公钥 私钥, 两者之间是有关联的
生成公钥和私钥
+ ssh-keygen -t rsa -C "mige@email.com"
git push git@github.com:migejiejie1/test01.git master
+ 通过ssh方式将当前分支的内容上传到远程的master分支上

# 在push和pull操作时
先pull, 在push

# push和pull简写方式
git remote add [远程仓库地址名称name] [远程github仓库地址]
例: git remote add origin git@github.com:migejiejie1/test.git 
+ 添加远程github仓库地址, 下次push和pull可直接简写为 
git push origin master 
+ 上传本地代码到远程仓库中
git pull origin master
+ 将远程仓库中的代码下载到本地
git push origin -u master 
+ 当我们在push时, 加上-u参数,那么在下一次push时, 我们只需要写上git push/git pull就能上传/下载我们的代码,
+ 加上-u之后, git会把当前分支与远程指定的分支通过[远程仓库地址名称name]进行关联
例: git push origin -u master
Everything up-to-date
Branch 'master' set up to track remote branch 'master' from 'origin'.
+ 分支“主”设置跟踪远程分支“主”从“原点”。  

# 将工作区中删除的文件恢复
git restore file_name.txt
# 将工作区和暂存区中的文件删除
git rm file_name.txt


```

