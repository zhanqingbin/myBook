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
echo 屏幕打印与文本输出  echo "hello world"   echo "hello world" > hello.txt  >>追加
cat 合并文件或查看文件内容  cat hello.txt  -n显示行数  -nE显示空白符  
合并：cat hello.txt myname.txt >> full.txt   cat full.txt
cat > test.txt << END(输入流的结束字符串是END)
tail 显示文件内容尾部  tail log.txt   tail -n 3 log.txt显示3行
tail -f log.txt  监听变化  有变化就打印控制台  ctrl+c退出监控
grep 文本过滤工具  grep immoo test.txt > out.txt
grep -v immoo test.txt  -v不包含
grep i...o test.txt  正则支持i开头o结尾的5个字符
ll | grep .txt  筛选txt文件
ll | grep -E "log[0-9]{1,5}.txt"  -E使用扩展的正则表达式

文件的打包和压缩
gzip:linux系统文件压缩程序 .gz
tar:将多个文件打包和压缩的工具 .tar
.tar.gz
压缩：tar zcvf tomcat.tar.gz /usr/local/tomcat
解压缩：tar zxvf tomcat.tar.gz -C /usr/local/tomcat   -C指定解压缩到哪个路径  默认是当前目录
z：通过gzip压缩或者解压
c：创建系的tar.gz文件
v：显示执行过程
f：指定压缩文件名称
x：解压缩tar.gz文件
-C：指定解压目录
unzip

安装卸载应用程序
rpm:"