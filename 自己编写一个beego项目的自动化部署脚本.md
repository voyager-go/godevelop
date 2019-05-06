#### 感觉每次打包部署到服务器好繁琐，于是自己写了一个脚本来简化操作
#### 顶部的一些变量请自行替换，并命名为xxx.sh
#### 本地环境 mac os 服务器环境 ubuntu
```
#!/bin/sh
user=root
targetIP=yourip
path=/root/go/src/yourapp/
packName=yourapp.tar.gz

echo "请稍候，即将进行打包操作..."
echo "终端正在执行的命令为 bee pack -be GOOS=linux"
bee pack -be GOOS=linux
if [ $? -eq 0 ]; then
    echo "恭喜您，项目打包成功..."
    echo "正在连接服务器，为您删除原有包..."
    ssh $user@$targetIP<<EOF
    cd $path
    echo "当前已经进入：" $path
    rm -rf $packName
    echo "删除成功..."
    quit
EOF
    echo "正在连接SFTP，即将为您上传，请耐心等待..."
    sftp $user@$targetIP<<EOF
        cd $path
        put -r $packName
        quit
EOF
    echo "已经为您更新项目包：" $packName
    echo "即将为您解包并更新项目文件..."
    ssh $user@$targetIP<<EOF
        cd $path
        echo "当前已经进入：" $path
        tar -zxvf $packName
        quit
EOF
    echo "命令执行完毕..."
else
    echo "打包失败，未知错误..."
fi
```
#### 扩展：可以使用sshpass来取代ssh工具，从而实现不用输入密码直接登录