# NFS

## 通过 NFS 共享目录

``` dot
digraph G{
  client1->server
  client2->server
}
```

## 操作步骤

1. 安装软件
2. 启动服务
3. 服务端共享目录
4. 客户端挂载共享目录

## 安装软件

server 和 client 都需要安装 rpcbind 和 nfs-utils. 需要 root 权限.

### CentOS 7

``` shell
## 安装
yum install -y nfs-utils rpcbind

## 启动服务 && 设置开机启动
systemctl start rpcbind && systemctl enable rpcbind

systemctl start nfs-server && systemctl enable nfs-server

## 创建目录
mkdir /elastic/backup
## 设置目录所有者
chown -R user1 /elastic/bakcup
```

## 服务端

### 共享目录

``` shell
### 格式1: 目录 可访问的网段(参数列表)
### 格式2: [共享的目录] [主机名1或IP1(参数列表)] [主机名2或IP2(参数列表)]
echo "/elastic/backup 192.168.39.0/24(rw,no_root_squash)" >> /etc/exports

## 刷新并使能配置
exportfs -a
## 查看服务端可挂载目录
showmount -e 192.168.39.10

```

#### 可访问的主机

* 指定 IP 的主机: 192.168.39.53
* 指定 域名 的主机: client.test.com 或 *.test.com
* 指定网段: 192.168.39.0/24 或 192.168.39.*
* 所有主机: *

#### 配置参数

类别 | 参数 | 说明
---- | ---- | ----
读写控制 | ro | 只读访问
^       | rw | 可读可写
是否同步 | sync | 所有数据在请求时写入
^       | async | 在写入数据前可以响应请求
安全端口控制 | secure | 通过 < 1024 的安全端口发送
^ | insecure | 通过 > 1024 端口发送
多用户写入控制 | wdelay | 如果多个用户要写入, 则归组写入(默认)
^ | no_wdelay | 如果多个用户要写入, 则立即写入, 当使用 async 参数时, 无需此设置
是否共享子目录 | hide | 不共享子目录
^ | no_hide | 共享子目录
检查父目录权限 | subtree_check | 如果共享的是/usr/bin之类的目录时, 强制 NFS 检查父目录的权限(默认)
^ | no_subtree_check |不检查父目录权限
uid, gid 映射 | all_squash | 共享文件的UID和GID映射为匿名用户 anonymous, 适合公用目录.
^ | no_all_squash | 保留共享文件的 UID 和 GID (默认)
root 用户映射 | root_squash | root 用户的所有请求映射成 anonymous 用户(默认)
^ | no_root_squash | root 用户具有完全管理访问权限
匿名用户 uid, gid | anonuid=xxx | 指定 NFS 服务器中 /etc/passwd 文件中匿名用户的 UID
^ | anongid=xxx | 指定 NFS 服务器中 /etc/passwd 文件中匿名用户的 GID

## 客户端

``` shell

## 查看可挂载目录
showmount -e 192.168.39.10

## 挂载
mount -t nfs 192.168.39.10:/elastic/backup /elastic/backup

## 设置开机自动挂载
echo "192.168.39.10:/elastic/backup /elastic/backup nfs defaults 0 0" >> /etc/fstab

## 查看挂载
df -h
```
