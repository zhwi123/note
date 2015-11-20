#日志配置

```
# off表示关闭(不记录)日志
error_log  off ;

# logs/error.log表示日志存储位置，notice表示日志格式名称
error_log logs/error.log notice;
# 同上，表示访问日志
access_log logs/access.log main;

# 配置日志格式，main表示日志格式名称，后面语句表示日志格式具体内容
log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';

# 日志格式配置项说明
# $remote_addr与$http_x_forwarded_for用于记录客户端的地址
# $remote_user用于记录客户端用户名称
# $time_local用于记录访问时间与时区
# $request用于记录请求的url与http协议
# $status用于记录请求状态，成功是200
# $body_bytes_sent用于记录发送给客户端文件主体内容大小
# $http_referer用于记录从哪个页面链接访问过来的
# http_user_agent记录客户端浏览器特征信息

# Nginx日志切割
# 将日志文件重命名：mv /usr/local/nginx/logs/access.log /usr/local/nginx/logs/access_20140610.log
# 通知Nginx重新生成日志文件：kill -USR1 `cat /usr/local/nginx/logs/nginx.pid`
# 借助crontab可以编写一个定时切割日志的脚本
vi /usr/local/nginx/sbin/cut_nginx_log.sh
# 脚本内容
# -------------------------------------------------------------------------------------

#!/bin/bash
# 脚本应在每日00:00运行
  
# Nginx日志存放目录
logs_path="/usr/local/nginx/logs/"
 
# 当前日期目录名称
yesterday=$(date -d "yesterday" +%Y%m)/
 
# 创建日志目录并重命名日志文件
[ -d ${logs_path}${yesterday} ] || mkdir -p ${logs_path}${yesterday}
mv ${logs_path}access.log ${logs_path}${yesterday}access_$(date -d "yesterday" +%d).log
 
kill -USR1 `cat ${logs_path}nginx.pid`

# -------------------------------------------------------------------------------------

# 设置每日00:00执行脚本
crontab -e
0 0 * * * /bin/bash /usr/local/nginx/sbin/cut_nginx_log.sh
```
