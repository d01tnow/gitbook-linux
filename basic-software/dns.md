# DNS 服务

本文先讲 DNS 服务的工作原理, 然后讲 Linux (CentOS7) 环境下的轻量级 DNS/DHCP 服务 dnsmasq 和生产级 DNS 服务 bind 的搭建与维护相关的工作. 

## 工作原理



## dnsmasq

### 安装



### 配置



### 维护



## Bind

### 安装



### 配置



### 维护

- 启动: systemctl start named
- 停止: systemctl stop named
- 重启: systemctl restart named
- 开机启动: systemctl enabled named
- 禁用开机启动: systemctl disable named
- 重新加载配置和区域: rndc reload
- 检查配置: named-checkconf
- 检查区域配置: named-checkzone "区域名称" "区域文件全名(带路径)" . e.g. : named-checkzone "39.168.192-in-addr.arpa" /var/named/192.168.39.zone
- 修改区域文件所属用户和组: chown root:named "zonefile"
- 测试: dig "域名" @dns-ip . e.g. : dig www.test.com @192.168.39.55
- 反向解析测试: dig -x "ip" @dns-ip . e.g. : dig -x 192.168.39.111 @192.168.39.55

