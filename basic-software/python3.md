# python3

安装 python3

## CentOS7

* 先[升级gcc](./gcc.md).
* 再[升级openssl](./openssl.md). 特别要注意 --openssldir 和 --prefix 要配置成一样: /usr/local/ssl. 后面编译 python3 支持 ssl 需要用到 openssl 的 include, lib 目录.
* 下载安装

``` shell
## 安装依赖包, 否则 install 报错 "no module named '_ctypes'"
sudo yum install -y zlib zlib-devel libffi-devel ncurses ncurses-devel bzip2 bzip2-devel readline readline-devel xz lzma xz-devel sqlite sqlite-devel gdbm gdbm-devel tk tk-devel
curl -O https://www.python.org/ftp/python/3.7.1/Python-3.7.1.tar.xz
tar xvJf Python-3.7.1.tar.xz
cd Python-3.7.1

vi Modules/Setup.dist
  ## 查找到 SSL
  /SSL
  ## 取消以下4行注释
  #SSL=/usr/local/ssl
  #_ssl _ssl.c \
  # -DUSE_SSL -I$(SSL)/include -I$(SSL)/include/openssl \
  # -L$(SSL)/lib -lssl -lcrypto
  
./configure --prefix=/usr/local/python3 --enable-shared CFLAGS=-fPIC
make
sudo make install
sudo ln -s /usr/local/python3/bin/python3 /usr/bin/python3
sudo ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3

## 配置动态库加载目录
sudo vi /etc/ld.so.conf.d/python3.conf
  /usr/local/python3/lib
sudo ldconfig -v | grep python
python3 --version
pip3 --version
```
