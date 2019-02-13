# gcc

升级 gcc

## CentOS7

CentOS 把最新的开发者工具包放到 centos-release-scl 的源仓库中, 名称是 devtools-6.
CentOS7 中升级 gcc 过程如下:

``` shell
sudo yum install -y centos-release-scl
sudo yum install -y devtoolset-6
scl enable devtoolset-6 bash
sudo yum list devtoolset-6\*
gcc -v
g++ -v
```
