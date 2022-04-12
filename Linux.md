# Linux

连接服务器
```C
ssh @root 34.45.677.333
password:*****
yum install nginx
nginx -v
nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok

nginx -c /etc/nginx/nginx.conf

cd /
mkdir projectName
scp -r ./dist root@34.45.677.333:/projectName

vim /etc/nginx/nginx.conf
// 下载nginx文件配置
scp root@34.45.677.333:/etc/nginx/nginx.conf /Users/Qben/Desktop

// 添加自己的server
server {
  listen   80 default_server;
  root     /projectName/dist;

  location / {
    if($uri ~ ^/((?!js)(?!img)(?!css)(?!fonts).)*$){
      rewrite .*/index.html break;
    }
    index index.html index.htm
  }
 }
nginx -s reload
```