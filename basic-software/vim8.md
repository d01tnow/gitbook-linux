# vim8

安装 vim8

## CentOS7

* 先[升级gcc](./gcc.md)
* 如果要支持 python3. 先[安装python3](./python3.md)
* 安装vim-x11
* 下载安装vim8

``` bash
## 安装 vim-X11 支持 X11 , clipboard, xterm_clipboard
sudo yum-builddep -y vim-X11
## 下载最新源码
git clone --depth=1 https://github.com/vim/vim.git && cd vim
## 先确认 python config 目录. 很重要
## CentOS7 默认的 python2.7.5 的 config 路径: /usr/lib64/python2.7/config
## python3 的 config : /usr/local/python3/lib/python3.7/config-3.7m-x86_64-linux-gnu/
## lua5.3 头文件默认安装到 /usr/local/include, 通过设置 LUA_PRIFIX=/usr/local 告知 vim 安装程序
## LDFLAGS=-L/usr/local/ssl/lib 是为了指示 gcc 链接器找到 python3.7m.so 链接的 ssl 动态库
LDFLAGS=-L/usr/local/ssl/lib LUA_PREFIX=/usr/local ./configure --prefix=/usr/local/vim8 \
  --with-features=huge \
  --enable-multibyte \
  --enable-cscope \
  --enable-luainterp=yes \
  --enable-pythoninterp=dynamic \
  --with-python-config-dir=/usr/lib64/python2.7/config \
  --enable-python3interp=dynamic \
  --with-python3-config-dir=/usr/local/python3/lib/python3.7/config-3.7m-x86_64-linux-gnu \
  --enable-gui=auto

make
sudo make install

sudo ln -s /usr/local/vim8/bin/vim /usr/bin/vim
```

### 排错

* 如果需要重新 configure . 先 make distclean. 否则有可能有缓存, 造成配置失败.
* 如果 ./configure 提示提示无法找到 python 或 python3 , 请确认 --with-python-config-dir 或 --with-python3-config-dir 是否正确. 另外, python3 的 lib 目录是否配置正确. ldconfig -v | grep python
* 如果找不到 lua, make distclean && LUA_PREFIX=/usr/local ./configure ... 默认的 LUA_PREFIX: /usr
