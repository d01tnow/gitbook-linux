# rsyslog

CentOS7 中使用 rsyslog 服务进行日志管理. 本文基于 rsyslog v8.24.
本文收集 rsyslog 的输出日志

## rsyslog  简介

rsyslog 是 syslog 的升级版.  提供了诸如过滤器，日志加密保护，各种配置选项，输入输出模块，支持通过 TCP 或者 UDP 协议进行传输等。

新版本 rsyslog [参考](https://www.sharpcode.cn/linux/rsyslog-rainerscript/) 推荐使用 rainerscript 进行配置.
下面配置中有传统的配置方式和 RainerScript 方式( 格式: 对象(对象参数) )

日志文件位置一般位于: /var/log/ 目录
linux 一般使用 logrotate 服务根据 /etc/logrotate.conf 和 /etc/logrotate.d/\*.conf 的配置进行日志转储, 避免日志文件过大, 占满磁盘.

## rsyslog 配置

```shell
# 查看帮助
man rsyslog.conf

rsyslog 的配置文件位置为: /etc/rsyslog.conf, /etc/rsyslog.d/\*.conf

### 全局配置

用来配置加载模块, 模板或权限等全局指令

```conf
# 加载模块
module(load="imuxsock") # provides support for local system logging
module(load="imklog")   # provides kernel logging support
# 模板
# 默认模板
$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
# 自定义模板
# 指定动态文件名
$template DynFile,"/var/log/system-%HOSTNAME%.log"

# 权限等其他配置

## 单条日志的最大长度
$MaxMessageSize 64k
$FileOwner syslog
$FileGroup adm
$FileCreateMode 0640
$DirCreateMode 0755
$Umask 0022
$PrivDropToUser syslog
$PrivDropToGroup syslog
```

rsyslog.conf 的基本格式

```conf
# 基本格式
# FILTER ACTION[;template]
# rsyslog 发现有符合 filter 规则的日志后, 执行 action
# ACTION;template 表示使用模板进行格式化
# 可以用 & 连接多个 ACTION
#
```

### ACTION

常用的 ACTION:

* 丢弃: ~
  * 例1: \*.\* ~ # 丢弃全部
* 普通文件: 以'/'开头的绝对路径文件. 默认每条日志都会刷盘. action 添加 '-' 前缀则由文件系统刷盘.
  * 例1, 默认格式: \*.\*     /var/log/file.log # log to a file with RFC3339 timestamps
  * 例2, 指定格式: \*.\* /var/log/traditionalfile.log;RSYSLOG_TraditionalFileFormat
* 终端或控制台: 等同于文件. 比如: /dev/console
* 管道: 以 '|' 开头
* 在线用户: 格式:  \*.\* 用户名 . * 表示所有用户.
* 远程主机:
  * @host[:port] # 使用 UDP 发送, 默认端口 514
  * @@host[:port] # 使用 TCP 发送, 默认端口10514
* 执行脚本: 以 '^' 开头的绝对路径文件 . 日志内容作为脚本的第一个参数

### FILTER

rsyslog 提供 3 种过滤器

* 基于设施/优先级的过滤器
* 基于属性的过滤器
* 基于表达式的过滤器

#### 基于设施/优先级的过滤器

基本语法: facility连接符号priority
facility 指定产生日志的子系统.
可选值:

| 值 | facility        | 含义                     |
| :-- | :-------------- | :---------------------- |
| 0  | kernel            | 内核相关消息         |
| 1  | user-level    | ssh,ftp登录相关日志      |
| 2  | mail        | 邮件相关  |
| 3  | system daemons          | 系统服务相关日志          |
| 4  | auth            | 认证/授权相关日志, login,ssh,su等操作 |
| 5  | syslog            | syslog 自身日志             |
| 6  | lpr          | 打印相关       |
| 7  | news            | 新闻组相关 |
| 8  | uucp            | 早期 UNIX to UNIX cp 相关日志 |
| 9  | cron            | 定时任务相关 |
| 10  | authpriv  | 与 auth 类似，但记录较多帐号私人的信息 |
| 11  | ftp daemon    |             |
| 12  | ntp            |             |
| 13  | log audit      |             |
| 14  | log alert      |             |
| 15  | clock daemon   | 时钟 |
| 16  | local0 ~ local7 | 保留的,本机用户使用  |
| * |  | 所有 facility |

连接符号:

* .   : 表示大于等于 priority 的日志
* .= : 表示等于 priority 的日志
* .!  : 表示非 priority 的日志

priority 用来表示日志级别. 下表中为日志级别. 从下到上, 级别从低到高. 一般的, 级别越高, 输出的信息越少.

| 值  | priority    | 含义                     |
| :-- | :---------- | :---------------------- |
| 0   | LOG_EMERG   | 会导致系统不可用的严重问题 |
| 1   | LOG_ALERT   | 必须马上处理的警告信息     |
| 2   | LOG_CRIT    | 比较严重的问题            |
| 3   | LOG_ERR     | 错误信息                 |
| 4   | LOG_WARNING | 警告信息                 |
| 5   | LOG_NOTICE  | 需要注意的信息            |
| 6   | LOG_INFO    | 一般信息                 |
| 7   | LOG_DEBUG   | 调试信息                 |

#### 基于属性的过滤器

基本语法

```conf
# 基本语法
# :PROPERTY, [!]COMPARE_OPERATION, "STRING"
# PROPERTY: 要比较的日志属性
# COMPARE_OPERATION: 比较操作, 见下表
# ! : 表取反
# "STRING": 比较的值
```

COMPARE_OPERATION 列表

|     操作     |                      描述                      |
| ------------ | --------------------------------------------- |
| contains     | 提供的字符串是否是属性值的一部分, 区分大小写       |
| contains_i   | 提供的字符串是否是属性值的一部分, **不**区分大小写 |
| isequal      | 字符串 == 属性                                  |
| startswith   | 属性值是否以字符串开头, 区分大小写                |
| startswith_i | 属性值是否以字符串开头, **不**区分大小写          |
| isempty      | 属性是否为空                                    |
| regex        | 字符串为正则表达式(POSIX BRE, 基本正则)          |
| eregex       | 字符串为正则表达式(POSIX BRE, 基本正则)          |

#### 基于表达式的过滤器

## RainerScript

### action() 语句

```rainerscript
# 加载 omfwd 输出模块, 以 upd 协议输出到 192.168.0.55:5140
action(
    type="omfwd"
    target="192.168.0.55"
    port="5140"
    protocol="udp"
    template="templ_json_lines"
)
```

### template() 语句

语法([参考](https://rsyslog.readthedocs.io/en/latest/configuration/templates.html)): template(parameters) { 描述信息列表 }

有效的[属性](https://rsyslog.readthedocs.io/en/latest/configuration/properties.html)

例子, 输出 json 格式:

```rainerscript
template(name="templ_json_lines" type="list" option.json="on") {
  constant(value="{")
  constant(value="\"logtime\":\"")
  property(name="timereported" dateFormat="rfc3339")
  constant(value="\",\"message\":\"")
  property(name="msg")
  constant(value="\",\"hostname\":\"")
  property(name="hostname")
  constant(value="\",\"fromhostip\":\"")
  property(name="fromhost-ip")
  constant(value="\",\"severity\":\"")
  property(name="syslogseverity-text")
  constant(value="\",\"facility\":\"")
  property(name="syslogfacility-text")
  constant(value="\",\"appname\":\"")
  property(name="programname")
  constant(value="\",\"procid\":\"")
  property(name="procid")
  constant(value="\"}\n")
}
```

### 系统默认的输出

```txt
/var/log/message    #标准系统错误信息；
/var/log/maillog    #邮件系统产生的日志信息；
/var/log/secure     #记录系统的登录情况；
/var/log/dmesg      #记录linux系统在引导过程中的各种记录信息；
/var/log/cron       #记录crond计划任务产生的时间信息；
/var/log/lastlog    #记录每个用户最近的登录事件；
/var/log/wtmp       #记录每个用户登录、注销及系统启动和停机事件；
/var/run/btmp       #记录失败的、错误的登录尝试及验证事件；
```
