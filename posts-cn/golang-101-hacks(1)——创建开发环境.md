注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译,[原文地址](https://nanxiao.gitbooks.io/golang-101-hacks/content/posts/how-to-build-go-development-environment.html)
创建Go开发环境是非常容易的，以Linux系统为例，你只需要从[https://golang.org/dl/](https://golang.org/dl/)
下载和你系统匹配的二进制包，然后解压包文件就OK了。（注意作者原文的下载的包文件版本有点旧 ，建议下载最新版本，目前最新版本是1.12了）
```
# wget https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz
# tar -C /usr/local/ -xzf go1.6.2.linux-amd64.tar.gz
```
返回将解压的包文件放在/usr/local目录下，就结束安装了，但然还有一些收尾工作需要做：
1 为了直接运行Go工具类命令（go,gofmt),需要把`/usr/local/go`设置到$PATH的环境变量中
```
# cat /etc/profile  
......
PATH=$PATH:/usr/local/go/bin
export PATH 
......
```
2 墙裂建议go的安装目录在linux是/usr/local/go在window是c:\Go，因为个路径地址是go的发行版本所默认的安装路径地址，否则你需要修改$GOROOT 这个环境变量。
```
# cat /etc/profile  
......
GOROOT=/path/to/go
export GOROOT
```
因此只有go没有在默认路径下 才需要修改$GOROOT这个环境变量值。
