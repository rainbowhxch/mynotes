# Bash (or Bourne Again shell)
GNU 出品，必属精品，在 Linux 下，Bash 是标准的 Shell。其实在 Bash 诞生之前在 Unix 环境下还有一个 Sh 存在，Bash 就是 Sh 的超集，且 Bash 完全兼容 Sh。注：本文的所有命令与脚本均在 Bash 下执行，在其他 Shell 中不能保证正常运行。

## Bash 启动文件
Bash 启动文件是在用户启动 Bash 时自动进行解释的配置文件。
启动文件包括如下情况：
- 当 Bash 以非登录模式(没加 --login 选项)启动时，`~/.bashrc` 被读取。
- 当 Bash 以登录模式 (附带 --login 选项)启动时，以下文件也会被读取：
  - `/etc/profile`
  - `~/.bash_profile`, `~/.bash_login` 或 `~/.profile`
  - `~/.bash_logout` (bash 退出时被自动执行)

## 有关 Bash Script 执行
### Bash Script 执行方式
- `./script_name.sh`
- `sh script_name.sh`
- `bash -x script_name.sh`
对于以上执行方式，父 Shell 会 Fork 出一个子 Shell，然后在子 Shell 中运行此 Bash Script，父 Shell 无法访问到子 Shell 中的变量，当脚本运行完毕后，状态被还原脚本被执行前。如果想以当前 Shell 执行脚本，可以使用：`source script_name.sh`，这样脚本生成的状态会被当前 Shell 保存下来。

### 指定 Shell 可执行文件位置
为了确定哪一个 Shell 执行脚本，脚本约定文件第一行注释标注 Shell 执行文件的位置。如：
```bash
#！/bin/bash
```
这也是以下内容的默认约定。

### 注释
注释以符号`#`开头，可单独成行，也可插入行尾，脚本运行时会被忽略。

### 以 Debug 模式执行
Bash自身有两种Debug方式：
1. 对整个脚本Debug：
```bash
bash -x script_name.sh
```
2. 对部分命令：
```bash
set -x			# activate debugging from here
commmands       # some commmands
set +x			# stop debugging from here
```

## Bash 环境
### 变量

## Copyright(C)
本文绝大部分内容来自Machtelt Garrels 所作的 [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/index.html)，文章涉及的一切版权归原作者所有，本文只是作者的提炼笔记记录，如果对你学习有所帮助，那么就再好不过了。
