# Dedian

## 更换源

```shell
# 备份
sudo cp /etc/apt/sources.list /etc/apt/sources.list_bak

# 清华源
sudo vi /etc/apt/sources.list

## ------ /etc/apt/sources.list ---------
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free
## ------ /etc/apt/sources.list ---------

# 如果遇到无法拉取 https 源的情况，请先使用 http 源(修改上面的 https 为 http)并安装
sudo apt install apt-transport-https ca-certificates

sudo apt update

```

## 安装软件

```shell
# git
sudo apt install git
sudo apt install fonts-powerline -y
```

### zsh

```shell
sudo apt install zsh

chsh -s /bin/zsh

# 安装 oh-my-zsh
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

# 修改主题
sed -i 's/robbyrussell/ys/g' ~/.zshrc

# 安装zsh-autosuggestions语法历史记录插件
git clone https://github.com/zsh-users/zsh-autosuggestions.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# 安装zsh-syntax-highlighting高亮插件
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# 启用插件
vi ~/.zshrc
# 查找到 plugins 行, /(git)
# 修改为 plugins=(git extract z zsh-syntax-highlighting zsh-autosuggestions)
# 保存. :x
# 重新加载 .zshrc
. ~/.zhsrc

```

## 开发工具

```shell
sudo apt install build-essential m4
g++ -v
sudo apt install cmake gdb ninja-build rsync


```
