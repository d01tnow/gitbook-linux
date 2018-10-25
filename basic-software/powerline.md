# powerline

美化提示信息和状态栏等. python 编写, 兼容 python2.6+, python3+环境. 支持多种 Linux 工具的提示和状态栏.

## 安装

* 安装依赖.
  * CentOS: sudo yum install -y epel-release && sudo yum install -y python-pip
  * Debian, Ubuntu, Mint: apt-get install python-pip
* 升级 pip: sudo pip install --upgrade pip
* 安装 powerline. sudo pip install powerline-status
* 安装 powerline 字体. git clone https://github.com/powerline/fonts && ./fonts/install.sh
* 配置 ~/.bashrc.

  ```shell

  echo -e "if [ -f \`which powerline-daemon\` ]; then \n \
    \tpowerline-daemon -q \n \
    \tPOWERLINE_BASH_CONTINUATION=1 \n \
    \tPOWERLINE_BASH_SELECT=1 \n \
    \tsource $(pip show powerline-status | awk '/Location:/ {print $2 "/powerline/bindings/bash/powerline.sh"}') \nfi" >> ~/.bashrc
  source ~/.bashrc
  ```

* 配置 vim

  ```shell
  echo -e "set nocompatible \n \
  set laststatus=2 \n \
  set t_Co=256 \n \
  set rtp+=$(pip show powerline-status | awk '/Location:/{print $2 "/powerline/bindings/vim"}')" >> ~/_vimrc
  ```
