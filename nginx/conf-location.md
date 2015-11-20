#Location配置

```
Location配置

# 静态文件访问配置，设置缓存，文件实际路径：html/static/静态文件
location ~ ^/(images|javascript|js|css|flash|media|static)/ {
	# 静态文件目录
	root html/static;
	# 静态文件缓存时间，单位d/h/m/s
	expires 30d;
	# 关闭日志
	# access_log off;
}

# 禁止访问.doc文件
location ~ /.doc {
	deny all;
}

# 配置下载
location /download/ {  
	# valid_referers none blocked server_names *.example.com;  
	if ($invalid_referer) {  
	   rewrite ^/ http://www.example.com/;  
	   return 403;  
	}  
	rewrite_log on;  
	rewrite /download/*/mp3/*.any_ext to /download/*/mp3/*.mp3  
	rewrite ^/(download/.*)/mp3/(.*)\..*$ /$1/mp3/$2.mp3 break;  
	root /spool/www;  
	autoindex on;  
	access_log off;  
} 

# 对/启用负载均衡   
location / {
	# 请求转向mysvr 定义的服务器列表
	proxy_pass  http://mysvr ;
	# 以下是一些反向代理的配置可删除.
	proxy_redirect off;
	# 后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
	proxy_set_header Host $host;
	proxy_set_header X-Real-IP $remote_addr;
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	# 允许客户端请求的最大单文件字节数
	client_max_body_size 10m;
	# 缓冲区代理缓冲用户端请求的最大字节数，
	client_body_buffer_size 128k;
	# nginx跟后端服务器连接超时时间(代理连接超时)
	proxy_connect_timeout 90;
	# 后端服务器数据回传时间(代理发送超时)
	proxy_send_timeout 90;
	# 连接成功后，后端服务器响应时间(代理接收超时)
	proxy_read_timeout 90;
	# 设置代理服务器（nginx）保存用户头信息的缓冲区大小
	proxy_buffer_size 4k;
	# proxy_buffers缓冲区，网页平均在32k以下的话，这样设置
	proxy_buffers 4 32k;
	# 高负荷下缓冲大小（proxy_buffers*2）
	proxy_busy_buffers_size 64k;
	# 设定缓存文件夹大小，大于这个值，将从upstream服务器传
	proxy_temp_file_write_size 64k;
} 
```
