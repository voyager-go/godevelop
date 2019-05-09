#### 国内使用go get 安装goquery会失败
##### package golang.org/x/net/html: unrecognized import path "golang.org/x/net/html" (https fetch: Get https://golang.org/x/net/html?go-get=1: dial tcp 216.239.37.1:443: i/o timeout)
#### 原因是缺少一个依赖包net
#### 首先获取 golang.org/x/net 包
```
mkdir -p $GOPATH/src/golang.org/x 
cd $GOPATH/src/golang.org/x 
git clone https://github.com/golang/net.git
```
#### 安装依赖包之后，再来获取goquery包
```
go get github.com/PuerkitoBio/goquery
```
