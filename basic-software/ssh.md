# ssh

## 基本用法

```shell
## 默认端口 22
ssh user_name@host
## 指定端口
ssh user_name@host -p port
```

### 通过配置使用

```shell
vi ~/.ssh/config

# 别名， 随意取, 后续可以使用 ssh test 方式登录
Host test
# 目标主机， 可以是 IP， 主机名，域名等
HostName target_host
# 端口号
Port 22
# 登录用户名
User root
# 登录目标机所需的 ID 文件, 不需要加 .pub 后缀
IdentityFile ~/.ssh/id_rsa
# 代理（跳板机方式）, 需要代理上安装 nc (netcat), 通过 nc 转发请求
# 还需要输入密码
# 配置完成后通过 "ssh-copy-id -i ~/.ssh/id_rsa test" 免密处理
# ProxyCommand 中的 -i ~/.ssh/id_rsa， 是登录代理的 ID 文件
ProxyCommand ssh -qaY -i ~/.ssh/id_rsa -p 12345 user@host 'nc %h %p'
```

## 免密

```shell
## 生成密钥/公钥对
## -t 是类型， 可以省略， 默认 rsa
## -f 是密钥/公钥对文件路径， 公钥会添加后缀 '.pub'
## -C 是注释
ssh-keygen -t rsa -f ～/.ssh/id_test -C "test key"

## 拷贝公钥到免密登录机器中
## -i 是公钥路径
## -p 是目标机 ssh 端口
## test@host 是登录到目标机的 用户@主机
ssh-copy-id -i ~/.ssh/id_test.pub -p 12345 test@host
## 后续会提示输入 test@host 的密码， 成功后n免密
## 如果已经在 ～/.ssh/config 中有 Host=test 的配置, 可以i直接使用
## ssh-copy-id -i ~/.ssh/id_test.pub test

## 免密过程实际是把公钥文件内容附加到目标机器的登录用户的 .ssh/authorized_keys 文件中, 该文件权限必须为 600, .ssh 目录权限为 700
## .ssh/know_host 存放已知的主机公钥清单
```
