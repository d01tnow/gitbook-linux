# centos7

## 安装

* 如果想最小安装, 但是要 GUI. 那么在安装时选择 Server with GUI.
* 如果选的是Minimal Install. 那么在装完后再安装图形界面

  ``` bash

  sudo yum groupinstall -y "X Window System"
  sudo yum groupinstall -y "GNOME Desktop"

  ## 启动图形界面
  startx

  ## 查看默认启动
  systemctl get-default
  ## 设置启动到图形界面, 在图形界面选择 Log out 回到 Shell
  #sudo systemctl set-default graphical.target
  ## 设置默认启动到shell 界面
  #sudo systemctl set-default multi-user.target
  ```

## yum

```bash
## 安装 epel 源
sudo yum install -y epel-release
## 备份 CentOS-Base.repo
sudo mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
sudo curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
echo -e "nameserver 114.114.114.114 \nnameserver 202.96.209.5"| sudo tee -a /etc/resolv.conf
sudo yum clean all
sudo yum makecache
```

## system 相关

### 开机启动网络

* ip addr # 查看网络情况, 找到 enp0 开头的网卡名, 例如: enp0s3
* ls /etc/sysconfig/network-scripts
* sudo sed -i 's/ONBOOT=no/ONBOOT=yes/' /etc/sysconfig/network-scripts/ifcfg-enp0s3

### 修改时区

* 不考虑系统发行版本差异使用以下命令. 不要用 cp 方式, java 读取的时区会不符合预期(不是 Linux 时区)
  > sudo ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
* centos7 提供的系统时间处理命令
  > timedatectl # 查看时间信息
  > timedatectl set-timezone Asia/Shanghai #用 timedatectl 设置时区
  > timedatectl set-local-rtc 1 # 将硬件时钟调整为与本地时钟一致, 0 为设置为 UTC 时间

### firewall

* firewall-cmd --state # 查看防火墙状态
* sudo systemctl stop firewalld # 关闭防火墙
* sudo systemctl disable firewalld # 开机禁用防火墙

### 主机名

* hostnamectl # 查看主机名
* hostnamectl set-hostname $hostname # 设置主机名

### 让 windows 通过主机名可以访问

* 先修改好主机名
* 安装 samba, 并启用服务

``` bash
sudo yum install -y samba && sudo systemctl start nmb && sudo systemctl enable nmb
```

### 升级内核

``` bash
## 查看内核版本
uname -sr
## 使用 ELRepo 仓库
sudo rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
sudo rpm -Uvh https://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
## 查看内核相关包
yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
## 安装主线稳定版(mainline-stable)
sudo yum --enablerepo=elrepo-kernel install -y kernel-ml
## 修改grub
sudo sed -i 's/GRUB_DEFAULT=saved/GRUB_DEFAULT=0/' /etc/default/grub
## 创建内核配置
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
## 重启
sudo reboot

## 查看内核版本, 确认是否成功
uname -sr
## 查看内核包
rpm -q kernel
## 安装 yum-utils
sudo yum install yum-utils
## 使用 yum-utils 中的 package-cleanup 卸载旧内核, 保留最新的 2 个内核
sudo package-cleanup --oldkernels --count=2

