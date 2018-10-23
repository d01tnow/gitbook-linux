# vim8

安装 vim8

## CentOS7

* 先[升级gcc](./gcc.md)
* 如果要支持 python3. 先[安装python3](./python3.md)
* 安装 ctags: sudo yum install -y ctags
* 下载安装vim8

``` bash
## 下载最新源码
git clone --depth=1 https://github.com/vim/vim.git
cd vim
## 先确认 python config 目录. 很重要
## CentOS7 默认的 python2.7.5 的 config 路径: /usr/lib64/python2.7/config
## python3 的 config : /usr/local/python3/lib/python3.7/config-3.7m-x86_64-linux-gnu/
./configure --prefix=/usr/local/vim8 \
  --with-features=huge \
  --enable-multibyte \
  --enable-cscope \
  --enable-luainterp=yes \
  --enable-pythoninterp=yes \
  --with-python-config-dir=/usr/lib64/python2.7/config/ \
  --enable-python3interp=yes \
  --with-python3-config-dir=/usr/local/python3/lib/python3.7/config-3.7m-x86_64-linux-gnu/ \
  --enable-rubyinterp=yes \
  --enable-perlinterp=yes

make
sudo make install

sudo ln -s /usr/local/vim8/bin/vim /usr/bin/vim
```

### 排错

* 如果需要重新 configure . 先 make distclean. 否则有可能有缓存, 造成配置失败.
* 如果 ./configure 提示提示无法找到 python 或 python3 , 请确认 --with-python-config-dir 或 --with-python3-config-dir 是否正确. 另外, python3 的 lib 目录是否配置正确. ldconfig -v | grep python
* 如果找不到 lua, make distclean && LUA_PREFIX=/usr/local ./configure ... 默认的 LUA_PREFIX: /usr
