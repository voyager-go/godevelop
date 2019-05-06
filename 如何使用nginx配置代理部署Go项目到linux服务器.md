#### 我用beego写的小项目已经结束了，今天刚部署到服务器上，简单记录一下部署过程，我这里是ubuntu 16 其他unix操作系统也类似。
##### www.blockchina.art
#### golang的安装配置 mysql的安装配置 nginx的安装配置这里不多讲 都是很基本的东西了
##### 首先把项目打包，命令为：bee pack -be GOOS=linux，打包完成后得到一个yourapp.tar.gz的包
##### 然后进入服务器，找到go的src目录，新建文件夹yourapp，接着把yourapp.tar.gz包传进来
##### 接着解包 tar -zxvf yourapp.tar.gz 解包完成后使用命令nohup ./yourapp &
##### 接下来开始配置nginx
##### 找到nginx的sites_enable文件夹，这里为了避免没用过nginx的新人会对配置冲突，我们直接删除default配置文件，或者迁移到其他目录
##### 新建sever.conf文件
```
server {

    listen      80;

    server_name  wwww.blockchina.art; # 你的域名

    charset utf-8;

    access_log  /root/go/log/poetry_access.log; # 日志文件，自己建一下，给个写入权限

    location /(css|js|fonts|img)/ {

        access_log off;

        expires 1d;

        root "/root/go/src/yourapp/static"; # 改成你自己的文件夹yourapp

        try_files $uri @backend;

    }

    location / {

        try_files /_not_exists_ @backend;

    }

    location @backend {

        proxy_set_header X-Forwarded-For $remote_addr;

        proxy_set_header Host            $http_host;

        proxy_pass http://127.0.0.1:8999; # 注意！这里的端口是你上面nohup执行项目所监听的端口，你需要自己查看一下到底监听的是哪个端口，可以用命令netstat -anp | grep yourapp来查看一下

    }

}
```
##### 接下来就是平滑加载配置与重启
```
nginx /usr/sbin/nginx -s reload
/etc/init.d/nginx restart
```
##### 大功告成