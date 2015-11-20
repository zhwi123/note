# Nginx运行用户
user nobody;

# 启动进程数，通常设置成和CPU核数相等的数量
worker_processes  1;

# Nginx运行时PID文件路径
pid /var/run/nginx.pid;

# 全局错误日志，具体配置参考：日志配置参考日志配置说明文档
# error_log  logs/error.log;
# error_log  logs/error.log  notice;
# error_log  logs/error.log  info;

events {
    # 工作模式有：use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]
	# 如果服务器操作系统是Linux 2.6+，应该使用epoll，如果是*BSD，则应使用kqueue
	use epoll;
	# 单个worker process进程的最大并发连接数，注意：最大客户数也由系统的可用socket连接数限制（~ 64K），所以不能超过这个值
    worker_connections  1024;
	# 当nginx收到一个新连接通知后接受尽可能多的连接
    # multi_accept on; 
}

# 设定http服务器，利用它的反向代理功能提供负载均衡支持
http {
	# include用于引入外部配置文件，如果server配置可以配置在其它文件中，在http模块配置中引入即可
    # 此处配置用于设定mime类型，类型由mime.type文件定义
    include mime.types;
	# 设置默认mime类型，默认：二进制流
    default_type  application/octet-stream;
    # 设定全局日志，由多个server共享此配置，具体配置参考：日志配置参考日志配置说明文档
    # access_log logs/host.access.log main;
	# 设置默认字符集
	# charset UTF-8;
    # sendfile指令指定nginx是否调用sendfile()函数(zero copy 方式)来输出文件，对于普通应用，必须设为 on，
	# 但如果用来进行下载等磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的uptime
    sendfile on;
	# tcp_nopush和tcp_nodelay两个指令设置为on用于防止网络阻塞
	# 告诉nginx在一个数据包里发送所有头文件，而不一个接一个的发送
    tcp_nopush on;
    tcp_nodelay on;
	# server_tokens并不会让nginx执行的速度更快，但它可以关闭在错误页面中的nginx版本数字，这样对于安全性是有好处的
	server_tokens off;

    # 连接超时时间
	# 设置客户端连接保持活动的超时时间，在超过这个时间之后，服务器会关闭该连接
    # keepalive_timeout  0;	# 0表示不限(猜的~)
    keepalive_timeout  65;
	# 设置客户端请求头读取超时时间。如果超过这个时间，客户端还没有发送任何数据，Nginx将返回Request time out (408)错误
	# client_header_timeout 60;
	# 设置客户端请求主体读取超时时间。如果超过这个时间，客户端还没有发送任何数据，Nginx将返回Request time out (408)错误，默认值是60
	# client_body_timeout 60;
	# 指定客户端的响应超时时间，这个设置不会用于整个转发器，而是在两次客户端读取操作之间，如果在这段时间内，客户端没有读取任何数据，nginx就会关闭连接
	# send_timeout 60;
    
	# 设置允许客户端请求的最大的单个文件字节数
	# client_max_body_size 128k;
	# 指定来自客户端请求头的headerbuffer大小。对于大多数请求，1K的缓冲区大小已经足够，如果自定义了消息头或有更大的Cookie，可以增加缓冲区大小。比如设置为32K
	# client_header_buffer_size 32k;
	# 指定客户端请求中较大的消息头的缓存最大数量和大小， 4为个数，128K为大小，最大缓存量为4个128K
	# large_client_header_buffers 4 128k;

	# 跟后端服务器连接的超时时间_发起握手等候响应超时时间
	proxy_connect_timeout 600;
	# 连接成功后，等候后端服务器响应时间，此时已经时行后端的排序之中等候处理
	proxy_read_timeout 600;
	# 后端服务器数据回传时间，就是在规定时间内后端服务器传完所有的数据
	proxy_buffer_size 16k;
	# 同上，告诉Nginx保存单个用户的几个Buffer最大所用空间
	proxy_buffers 4 32k;
	# 如果系统很忙的时候可以申请更大的proxy_buffers，官方推荐*2
	proxy_busy_buffers_size 64k;
	# proxy 缓存临时文件大小
	proxy_temp_file_write_size 64k;
	
    # 开启gzip压缩，具体配置参考：GZIP配置说明
    # gzip on;

	# 引入外部配置文件
    # include conf/www.conf.d/*.conf;

    # 设定负载均衡的服务器列表
	# 目前支持四种调度算法，后两种需要第三方组件支持
	# 1、轮询(默认)：每个请求按时间顺序逐一分配到不同的后端服务器，如果后端某台服务器宕机，故障系统被自动剔除，使用户访问不受影响
	# 2、weight：指定轮询权值，weight值越大，分配到的访问机率越高，主要用于后端每个服务器性能不均的情况下
	# 3、ip_hash：每个请求按访问IP的hash结果分配，这样来自同一个IP的访客固定访问一个后端服务器，有效解决了动态网页存在的session共享问题
	# 4、fair：比上面两个更加智能的负载均衡算法。此种算法可以依据页面大小和加载时间长短智能地进行负载均衡，也就是根据后端服务器的响应时间来分配请求，响应时间短的优先分配。Nginx本身是不支持fair的，如果需要使用这种调度算法，必须下载Nginx的upstream_fair模块
	# 5、url_hash：按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，可以进一步提高后端缓存服务器的效率。Nginx本身是不支持url_hash的，如果需要使用这种调度算法，必须安装Nginx 的hash软件包
	# 注意，当负载调度算法为ip_hash时，后端服务器在负载均衡调度中的状态不能是weight和backup
    upstream www.domain.net {
		# ip_hash ;
		# weigth参数表示权值，权值越高被分配到的几率越大
		server 192.168.9.1:8080 weight=5 down;
		server 192.168.9.1:8090 weight=1 backup;
		server 192.168.9.2:8080 weight=6;
		# 可以指定服务器负载均衡中调度状态，常用状态有：
		# down：表示当前的server暂时不参与负载均衡
		# backup：预留的备份机器。当其他所有的非backup机器出现故障或者忙的时候，才会请求backup机器，因此这台机器的压力最轻
		# max_fails：允许请求失败的次数，默认为1。当超过最大次数时，返回proxy_next_upstream 模块定义的错误
		# fail_timeout：在经历了max_fails次失败后，暂停服务的时间。max_fails可以和fail_timeout一起使用
    }

	# 虚拟主机配置，可以配置多个
	server {
		# 侦听80端口
        listen 80;
        # 定义server_name，即访问域名
        server_name www.domain.net;
        # 设定虚拟主机的访问日志，参考日志配置
        # access_log logs/www.domain.net/access.log  main;

		# 用于URL匹配，参考location配置说明
		location / {
			# 配置匹配URL根目录位置
			root index;
			# 配置首页文件名称，可以配置多个
			index index.html index.htm;
        }

		# 配置404状态码页面
		error_page 404 /404.html;
		# 定义50x状态码页面
		error_page   500 502 503 504 /50x.html;  
		# 定义50x.html访问路径：http://www.domain.net/50x.html
		location = /50x.html {
			root index;
		}
		
		# 设定查看Nginx状态的地址(未明确)
		location /NginxStatus {   
				stub_status on;   
				access_log on;   
				#auth_basic "NginxStatus";   
				#auth_basic_user_file conf/htpasswd;
                allow 222.66.154.110;   # 公司电脑网络出口IP
                allow 127.0.0.1;
                deny all;
		} 
     
	}
	 
}
