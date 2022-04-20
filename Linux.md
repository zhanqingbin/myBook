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
Linux命令格式
命令 【参数选项】 【文件或路径】

cd 切换目录
pwd 查看当前目录
ls、ll 显示目录内容
mkdir 创建目录 -p创建多级目录 -v显示创建过程  -pv联合使用
cp 复制文件与目录    cp 路径1 路径2           复制文件夹cp -r 路径1 路径2 
mv 移动或重命名文件  mv 文件命 新文件名   mv 文件名 路径   mv 目录（文件夹） 路径
rm 删除文件或目录  rm 文件名     -f 强制删除     rm -rf 目录
find 查找目录或文件  find / -name *.exe

vim编辑
vim 【选项】【文件】
i
Esc
delete或x 删除单个字符
dd 删除整行
/str 全文查找str字符串，n下一个，N前一个
:% s/old/new/g 替换文件内所有old字符串为new
u 撤销最后一次操作
:wq :wq! 退出并保存，只读文件需要加!
:q! 强制退出放弃保存

Linux文本工具
echo pin