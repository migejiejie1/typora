```shell
要手动重新启动Jenkins，您可以使用以下任一命令(通过在浏览器中输入其URL)：

http://[jenkins-server]/[command]
[command] 可以是：

exit 关闭 jenkins
restart 重启 jenkins
reload 重新加载配置

例:
(jenkins_url)/safeRestart - 允许所有正在运行的作业完成。重新启动完成后，新作业将保留在队列中以运行。
(jenkins_url)/restart - 强制重新启动而不等待构建完成。
```

