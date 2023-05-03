在 Bash 脚本中，使用 set -x 去调试输出（或者使用它的变体 set -v，它会记录原始输入，包括多余的参数和注释）。尽可能地使用严格模式：使用 set -e 令脚本在发生错误时退出而不是继续运行；使用 set -u 来检查是否使用了未赋值的变量；试试 set -o pipefail，它可以监测管道中的错误。当牵扯到很多脚本时，使用 trap 来检测 ERR 和 EXIT。一个好的习惯是在脚本文件开头这样写，这会使它能够检测一些错误，并在错误发生时中断程序并输出信息：

      set -euo pipefail
      trap "echo 'error: Script failed: see failed command above'" ERR