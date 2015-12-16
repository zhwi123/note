# Redis 持久化

## Redis备份、恢复  

### Redis备份  
```
# SAVE命令用于备份Redis
$ 127.0.0.1:6379> SAVE
1503:M 16 Dec 19:36:18.423 * DB saved on disk
OK
```
该命令将在`Redis`安装目录中创建`dump.rdb`文件，使用`BGSAVE`命令可以在后台备份
```
$ 127.0.0.1:6379> BGSAVE
1503:M 16 Dec 19:39:14.016 * Background saving started by pid 1519
Background saving started
127.0.0.1:6379> 1519:C 16 Dec 19:39:14.024 * DB saved on disk
                                                             1519:C 16 Dec 19:39:14.024 * RDB: 6 MB of memory used by copy-on-write
                                                   1503:M 16 Dec 19:39:14.108 * Background saving terminated with success
```

### Redis恢复  
如果需要恢复数据，只需将备份文件(`dump.rdb`)移动到`Redis`安装目录并启动服务即可。可以使用`CONFIG`命令获取`Redis`目录
```
$ 127.0.0.1:6379> CONFIG GET dir
1) "dir"
2) "/home/likun"
```

## Redis持久化
### 快照(snapshots)
缺省情况情况下，`Redis`把数据快照存放在磁盘上的二进制文件中，文件名为`dump.rdb`。可以配置`Redis`的持久化策略，例如数据集中每N秒钟有超过M次更新(可以设置多个规则)，就将数据写入磁盘，也可以手工调用命令SAVE或BGSAVE来生成快照文件。
```
# Save the DB on disk:
#
#   save <seconds> <changes>
#
#   Will save the DB if both the given number of seconds and the given
#   number of write operations against the DB occurred.
#
#   In the example below the behaviour will be to save:
#   after 900 sec (15 min) if at least 1 key changed
#   after 300 sec (5 min) if at least 10 keys changed
#   after 60 sec if at least 10000 keys changed
#
#   Note: you can disable saving completely by commenting out all "save" lines.
#
#   It is also possible to remove all the previously configured save
#   points by adding a save directive with a single empty string argument
#   like in the following example:
#
#   save ""

save 900 1
save 300 10
save 60 10000
```
`dump.rdb`文件保存路径配置
```
# The filename where to dump the DB
dbfilename dump.rdb

# The working directory.
#
# The DB will be written inside this directory, with the filename specified
# above using the 'dbfilename' configuration directive.
#
# The Append Only File will also be created inside this directory.
#
# Note that you must specify a directory here, not a file name.
dir ./
```
#### 工作机制
1. `Redis`会fork一个子进程
2. 子进程将数据写到磁盘上一个临时`RDB`文件中
3. 当子进程完成写临时文件后，将原来的`RDB`替换掉，这样的好处就是可以`copy-on-write`

#### `RDB`优点
`RDB`是一个非常紧凑(compact)的文件，它保存了`Redis`在某个时间点上的数据集，这种文件非常适合用于进行备份，另外`RDB`在恢复大数据集时的速度比`AOF`的恢复速度要快。
#### `RDB`缺点
因为`RDB`文件需要保存整个数据集的状态，所以在大数据量时操作非常耗时，在这种情况下， 一旦发生故障停机，可能会丢失好几分钟的数据。

### APPEND ONLY MODE(`AOF`)
快照模式并不可靠，所以对于要求高可靠性的应用来说，快照模式不是一个合适的选择，这时可以选用Append-only文件模式。
```
############################## APPEND ONLY MODE ###############################

# By default Redis asynchronously dumps the dataset on disk. This mode is
# good enough in many applications, but an issue with the Redis process or
# a power outage may result into a few minutes of writes lost (depending on
# the configured save points).
#
# The Append Only File is an alternative persistence mode that provides
# much better durability. For instance using the default data fsync policy
# (see later in the config file) Redis can lose just one second of writes in a
# dramatic event like a server power outage, or a single write if something
# wrong with the Redis process itself happens, but the operating system is
# still running correctly.
#
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
#
# Please check http://redis.io/topics/persistence for more information.

appendonly no

# The name of the append only file (default: "appendonly.aof")

appendfilename "appendonly.aof"
```
将appendonly配置修改为yes，即可开启`AOF`持久化模式，其它持久化频次通过appendfsync参数来控制
```
# The fsync() call tells the Operating System to actually write data on disk
# instead of waiting for more data in the output buffer. Some OS will really flush
# data on disk, some other OS will just try to do it ASAP.
#
# Redis supports three different modes:
#
# no: don't fsync, just let the OS flush the data when it wants. Faster.
# always: fsync after every write to the append only log. Slow, Safest.
# everysec: fsync only one time every second. Compromise.
#
# The default is "everysec", as that's usually the right compromise between
# speed and data safety. It's up to you to understand if you can relax this to
# "no" that will let the operating system flush the output buffer when
# it wants, for better performances (but if you can live with the idea of
# some data loss consider the default persistence mode that's snapshotting),
# or on the contrary, use "always" that's very slow but a bit safer than
# everysec.
#
# More details please check the following article:
# http://antirez.com/post/redis-persistence-demystified.html
#
# If unsure, use "everysec".

# appendfsync always
appendfsync everysec
# appendfsync no
```
* 当设置appendfsync为no的时候，Redis不会主动调用fsync去将AOF日志内容同步到磁盘，所以这一切就完全依赖于操作系统的调试了。对大多数Linux操作系统，是每30秒进行一次fsync，将缓冲区中的数据写到磁盘上。
* 当设置appendfsync为everysec的时候，Redis会默认每隔一秒进行一次fsync调用，将缓冲区中的数据写到磁盘。但是当这一 次的fsync调用时长超过1秒时。Redis会采取延迟fsync的策略，再等一秒钟。也就是在两秒后再进行fsync，这一次的fsync就不管会执行多长时间都会进行。这时候由于在fsync时文件描述符会被阻塞，所以当前的写操作就会阻塞。
* 当设置appendfsync为always时，每一次写操作都会调用一次fsync，这时数据是最安全的，当然，由于每次都会执行fsync，所以其性能也会受到影响。

**注：** 建议采用 appendfsync everysec（缺省方式），快照模式可以和AOF模式同时开启，互补影响。

#### 工作机制
`AOF`文件是可识别的纯文本文件，它的内容就是一个个的`Redis`标准命令，运作方式是不断地将命令追加到文件的末尾。

#### 修复`AOF`文件
由于某些原因导致了`AOF`损坏，`Redis`无法再加载这个`AOF`，可以使用`redis-check-aof`工具来修复
* 首先做一个`AOF`文件的备份，复制到其他地方
* 修复原始`AOF`文件，执行`redis-check-aof –-fix`，可以通过`diff –u`命令来查看修复前后文件不一致的地方
* 重启`Redis`服务

### AOF重写
每一条写命令都生成一条日志，`AOF`文件会很大。`AOF`重写是重新生成一份`AOF`文件，新的`AOF`文件中一条记录的操作只会有一次，而不像一份老文件那样，可能记录了对同一个值的多次操作。通过`BGREWRITEAOF`命令来实现`AOF`文件重写。

当`Redis`启动时， 如果`RDB`持久化和`AOF`持久化都被打开了，那么程序会优先使用`AOF`文件来恢复数据集，因为`AOF`文件所保存的数据通常是最完整的。

更多`Redis`持久化相关信息，请访问：<http://www.redis.cn/topics/persistence.html>查看
