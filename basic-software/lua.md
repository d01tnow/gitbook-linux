# lua

安装 lua

## CentOS7

* 到[官网](http://www.lua.org/download.html) 下载

``` shell
curl -R -O http://www.lua.org/ftp/lua-5.3.5.tar.gz
tar zxf lua-5.3.5.tar.gz && cd lua-5.3.5 && make linux test
sudo make install
lua -v
## lua 头文件默认安装在: /usr/local/include
## lua 库文件默认安装在: /usr/local/lib
```

* 如果报错. 尝试先安装依赖. 我先[升级gcc](./gcc.md), 没报错.

``` shell
sudo yum install -y libtermcap-devel ncurses-devel libevent-devel readline-devel
```
