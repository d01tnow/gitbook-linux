# 常用软件

## 终端

sudo apt-get install terminator

sudo apt-get install guake

sudo apt-get install tmux

## 开发环境

### CPP

#### 更新 CMake

````````` go
``` go
sudo apt-get autoremove cmake
sudo apt-get install build-essential
wget https://cmake.org/files/LatestRelease/cmake-3.16.1.tar.gz

tar xf cmake-3.16.1.tar.gz
cd cmake-3.16.1
./configure
make
sudo make install

export PATH=/usr/local/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH

```

