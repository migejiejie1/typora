```shell
将 Jenkins Shell 返回的字符串处理为字符数组

// Jenkinsfile
// 忽略 stage, steps 等其他无关步骤
...

scripts {
  // 将 Shell 返回字符串赋给 owners 这个变量。注意在 $ 前面需要加上 \ 进行转义。
  def owners = sh(script: "l=\$(ls -a fail-list-*) && for f in \$l; do f=\${f#fail-list-}; f=\${f%.txt}; echo \$f ; done;", returnStdout:true).trim()

  // 查看 owners 数组是否为空，isEmpty() 是 groovy 内置方法。
  if ( ! owners.isEmpty() ) {
    // 通过 .split() 对 owners string 进行分解，返回字符串数组。然后通过 .each() 对返回的字符串数组进行循环。
    owners.split().each { owner ->
      // 打印最终的用户返回
      println "owner is ${owner}"

      // 发送邮件，例子
      email.SendEx([
          'buildStatus'  : currentBuild.currentResult,
          'buildExecutor': "${owner}",
          'attachment'   : "fail-list-${owner}.txt"
      ])
    }
  }
}
```

