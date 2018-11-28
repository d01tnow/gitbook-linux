# Rsync

本文介绍文件同步工具 rsync.

rsync 可以在中断之后恢复传输；它只传输源文件和目标文件之间不一致的部分；rsync 可以执行完整备份或增量备份。更棒的是，在所有风格的 UNIX 上都可以使用 rsync，包括 Mac OS X，所以很容易连接任何系统。

rsync 有两种同步方式: 使用 shell(ssh 或 rsh) 或者使用 rsync 的 daemon 方式. 如果是使用 shell 方式, 推荐使用 ssh, 并采用**免密**方式.
它和 scp 的主要区别是: rsync 是同步工具, scp 是拷贝工具. rsync 不支持远程到远程的同步, scp 支持远程到远程的拷贝.

## 常用参数

``` yml
---
-v: 展示详细的同步信息
-a: 归档模式，相当于 -rlptgoD
  -r: 递归目录
  -l: 同步软连接文件
  -p: 保留权限
  -t: 将源文件的"modify time"同步到目标机器
  -g: 保持文件属组
  -o: 保持文件属主
  -D: 和--devices --specials一样，保持设备文件和特殊文件
-z: 发送数据前，先压缩再传输
-H: 保持硬链接
-n: 进行试运行，不作任何更改
-P: same as --partial --progress
  --partial: 支持断点续传
  --progress: 展示传输的进度
--delete: 如果源文件消失，目标文件也会被删除
--delete-excluded: 指定要在目的端删除的文件
--delete-after: 默认情况下，rsync是先清理目的端的文件再开始数据同步；如果使用此选项，则rsync会先进行数据同步，都完成后再删除那些需要清理的文件。
--exclude=PATTERN: 排除匹配PATTERN的文件
--exclude-from=FILE: 如果要排除的文件很多，可以统一写在某一文件中
-e ssh: 使用SSH加密隧道传输, 还可以指定 ssh 端口, -e 'ssh -p 1234'

```

## 工作模式

基本用法

``` shell

# 列表模式, 类似 ls -l : 仅有 SRC
rsync [OPTION]... SRC

# 本地 shell 模式: 本地 <-> 本地 : 当 SRC 和 DEST 路径信息都不包含有单个冒号”:”分隔符时就启动这种工作模式
rsync [OPTION]... SRC [SRC]... DEST
# 远程 shell 模式: 远程 <-> 本地 : 当 SRC 或 DEST 地址路径包含单个冒号”:”分隔符时启动该模式。
rsync [OPTION]... SRC [SRC]... [USER@]HOST:DEST
rsync [OPTION]... [USER@]HOST:SRC [DEST]

# deamon 模式: 客户端 <-> 服务器 : 当 SRC 或 DEST 地址路径包含双冒号”::”分隔符或使用 rsync:// 时启动该模式。
rsync [OPTION]... SRC [SRC]... [USER@]HOST::DEST
rsync [OPTION]... [USER@]HOST::SRC [DEST]

rsync [OPTION]... SRC [SRC]... rsync://[USER@]HOST[:PORT]/DEST
rsync [OPTION]... rsync://[USER@]HOST[:PORT]/SRC [DEST]


```
