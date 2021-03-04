# fabric 1.4 mac m1 安装

fabric1.4 的base image是没有arm64架构的需要自己去编译，但是我按网上很多编译手法，要么网络
出问题了，要么又是些其他神奇的错误，难道就没有大佬把编译好的镜像上传的吗，所幸被我找到了。https://hub.docker.com/u/ptunstad


此时我的m1还是只能用docker的预览版本，pull下来镜像后重新打tag，

```shell
docker tag ptunstad/fabric-couchdb:arm64-1.4.1   hyperledger/fabric-couchdb:latest

docker tag ptunstad/fabric-orderer:arm64-1.4.1   hyperledger/fabric-orderer:latest

docker tag ptunstad/fabric-tools:arm64-1.4.1   hyperledger/fabric-tools:latest

docker tag ptunstad/fabric-ccenv:arm64-1.4.1   hyperledger/fabric-ccenv:latest


docker tag ptunstad/fabric-couchdb:arm64-1.4.1   hyperledger/fabric-couchdb:latest

docker tag ptunstad/fabric-zookeeper:arm64-1.4.1   hyperledger/fabric-zookeeper:latest

docker tag ptunstad/fabric-kafka:arm64-1.4.1   hyperledger/fabric-kafka:latest

docker tag ptunstad/fabric-peer:arm64-1.4.1   hyperledger/fabric-peer:latest


docker tag ptunstad/fabric-baseimage:arm64-0.4.15   hyperledger/fabric-baseimage:arm64-0.4.15

docker tag ptunstad/fabric-basejvm:arm64-0.4.15   hyperledger/fabric-basejvm:arm64-0.4.15

docker tag ptunstad/fabric-baseos:arm64-0.4.15   hyperledger/fabric-baseos:arm64-0.4.15
```
    
然后需要编译下fabric产生1.4的可执行文件，

```shell
## 此处为我自己fork的仓库，可以替换为官网
git clone https://github.com.cnpmjs.org/HobbyBear/fabric.git

cd fabric

git checkout release-1.4

make native

```

这样可执行文件和镜像都有了，现在来编译下fabric ca

```shell
git clone https://github.com.cnpmjs.org/hyperledger/fabric-ca.git

cd fabric-ca

make 

```

很遗憾make报错了，，
Fabric make peer-docker: bzip2 data invalid:bad magic value
查阅了下，貌似是mac上的解压缩工具不对，
解决如下
```shell
brew install gnu-tar
GNUBIN=/usr/local/opt/gnu-tar/libexec/gnubin
export PATH=$GNUBIN:$PATH   #GNUBIN要在PATH前，否则就会优先搜索到系统的bsdtar

```

测试tar版本
```shell
➜  ~ tar --version
tar (GNU tar) 1.32
Copyright (C) 2019 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Written by John Gilmore and Jay Fenlason.

```

再重新执行make即可。

然后后来我用go sdk安装区块链的时候发现报了个很奇怪的错，
unix /host/var/run/docker.sock: connect: no such file or directory 


查阅发现是mac的新版本的docker的bug需要去掉个默认配置

打开mac的docker面板去掉默认勾选的use grpc full for file sharing