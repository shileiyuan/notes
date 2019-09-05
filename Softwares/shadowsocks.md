### 安装组件
> yum install m2crypto python-setuptools
> easy_install pip
> pip install shadowsocks

### 配置服务器参数
> vi /etc/shadowsocks.json

### 写入配置：
```json
{
  "server":"0.0.0.0",
  "server_port":443,
  "local_address": "127.0.0.1",
  "local_port":1080,
  "password":"123456",
  "timeout":300,
  "method":"aes-256-cfb",
  "fast_open": false
}
```
server填入vps地址，server_port和password自定义，其他的不用改

### 配置防火墙
> \#安装防火墙
> yum install firewalld
> \#启动防火墙
> systemctl start firewalld

### 开启防火墙相应的端口
> \# 端口号是上面自己设置的端口
> firewall-cmd --permanent --zone=public --add-port=443/tcp\
> firewall-cmd --reload

### 启动shadowsocks服务
> ssserver -c /etc/shadowsocks.json

如果想让该服务在后台运行，使用
> nohup ssserver -c /etc/shadowsocks.json &

### 退出nohup任务
> \# 查看当前 shell 终端的后台运行任务进程信息
> \$ jobs
> [1]+ Running nohup java -jar adapter-minisite.jar /tomcat-1 /tomcat-2 > logs.txt 2>&1 &
> \# 杀掉任务号
> \$ kill %1
> \# 或者找到pid
> \$ jobs -l
> [1]+ 11076 Running nohup java -jar adapter-minisite.jar /tomcat-1 /tomcat-2 > logs.txt 2>&1 &
> \$ kill 11076
> \# 或者
> \$ fg %n  # 置为前端运行
> Ctrl + c # 退出

jobs只能查看当前终端运行的任务，如果在另一个终端中，可以用ps命令查看
> ps -aux | grep 'ssserver' | grep -v grep



## 一键搭建ssr

### 安装
```bash
wget --no-check-certificate https://raw.githubusercontent.com/quniu/shadowsocks-all/master/install/shadowsocks-all.sh

chmod +x shadowsocks-all.sh

./shadowsocks-all.sh 2>&1 | tee shadowsocksR.log

```
然后根据提示安装即可

### 卸载
`./shadowsocks-all.sh uninstall`

### 服务命令
```shell
# start | stop | restart | status
# 重启
service shadowsocks-r restart
# 停止
service shadowsocks-r stop
# 启动
service shadowsocks-r start
# 查看运行状态
service shadowsocks-r status
# 也可以这样
/etc/init.d/shadowsocks-r restart
```

### 配置文件路径
`/etc/shadowsocks-r/config.json`

推荐的最佳协议：auth_sha1_v4_compatible及其它

推荐的最佳混淆：tls1.2_ticket_auth_compatible及其它


wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log



144.34.235.96
11575
mafan2011
origin
plain
aes-256-cfb