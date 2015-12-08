# Redis简介及安装示例

## CentOS-6.x编译安装Redis  
```
wget http://download.redis.io/releases/redis-3.0.5.tar.gz
tar zxvf redis-3.0.5.tar.gz
cd redis-3.0.5
make
make test
make install
```
执行`make test`需要使用`tcl`
```
sudo yum install tcl
```

