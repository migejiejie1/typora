```shell
https://www.cnblogs.com/zoulongbin/p/5773330.html

cat << EOF > /usr/share/httpd/noindex/index.html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>CentOS 7 镜像</title>

<script>document.createElement("myHero")</script>
<style>
myHero {
        display: block;
        background-color: #ddd;
        padding: 10px;
        font-size: 20px;
} 
</style> 

</head>
<body>
    <h1>简介</h1>
    <hr>
    <p>CentOS，是基于 Red Hat Linux 提供的可自由使用源代码的企业级 Linux 发行版本，是一个稳定，可预测，可管理和可复制的免费企业级计算平台。</p>
    <hr>
    <br>
    <br>

        <h1>CentOS 7 配置内部YUM源</h1>
    <br>
        <h2>1、备份</h2>
        <myHero>mv /etc/yum.repos.d/* /opt/yum/</myHero>
    <br>
        <h2>2、下载新的 CentOS-Base.repo 到 /etc/yum.repos.d/ </h2>
        <myHero>curl -o /etc/yum.repos.d/CentOS-Base.repo http://10.10.10.20/repo/CentOS-Base.repo</myHero>
    <br>
        <h2>3、运行 yum makecache 生成缓存</h2>
    <br>
        <h2>4、运行 yum repolist   查看已经生成缓存</h2>
    <br>
    <br>

</body>
</html>
EOF
```

