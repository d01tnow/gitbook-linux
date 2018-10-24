# openssl

升级 openssl

## CentOS7

### 下载最新稳定版本

在 [openssl](https://www.openssl.org/source/) 查看发布版本
参考[配置说明](https://wiki.openssl.org/index.php/Compilation_and_Installation#PREFIX_and_OPENSSLDIR)

``` shell
# 更新 zlib, 安装 zlib 开发包
sudo yum install -y zlib zlib-devel

## 下载
curl -LO https://www.openssl.org/source/openssl-1.1.1.tar.gz
## 解压
tar zxf openssl-1.1.1.tar.gz
cd openssl-1.1.1
## --openssldir 设置安装目录, --prefix 为lib, include, bin 目录的前缀. 两个目录设置成一样.
## 两个都指定下, 以免后面配置时候不好找.
## 使用 zlib 静态库, 生成动态链接库, 使用 fPIC 编译选项
./config --openssldir=/usr/local/ssl --prefix=/usr/local/ssl zlib shared CFLAGS=-fPIC
make && sudo make install
## 替换旧版本
sudo mv /usr/bin/openssl /usr/bin/openssl.bak
sudo mv /usr/include/openssl /usr/include/openssl.bak
sudo ln -s /usr/local/ssl/bin/openssl /usr/bin/openssl
sudo ln -s /usr/local/ssl/include/openssl /usr/include/openssl
sudo vi /etc/ld.so.conf.d/openssl.conf
  /usr/local/ssl/lib
sudo ldconfig -v | grep openssl
openssl version -a
```
