# Redis基本命令

```
Redis命令行工具
redis-cli

# 测试Redis服务器是否可以连通
PING

# 认证命令
AUTH 密码

# 查看Redis中有哪些Key存在(不能在生产环境用)
KEYS *
DBSIZE
FLUSHALL	# 清空Redis，慎用

Redis数据类型：String、List、Set、Hash、ZSet

[K/V(STRING)]
SET user:name jackson		# 添加键值对到Redis，user:name是键、jackson是值
GET user:name				# 获取user:name保存的信息
DEL key1 key2 ...			# 删除键，可以删除多个，命令返回成功删除的键个数
EXISTS key1 key2 ...		# 判断键是否存在
TTL	key						# 查看键过期时间，返回-1表示永不过期、-2表示键不存在、返回大于0的数字，表示缓存过期时间(单位：秒)
EXPIRE key 60				# 设置Key缓存过期时间，单位：秒，到过期时间后，Key自动删除
EXPIREAT key 时间			# 表示Key缓存过期时刻，时间是一个表示时刻的整数
MSET user:name jackson user:email jackson@xxx.net user:birthday 2000-7-1	# MSET用于同时设置多个键值，参数以key value key value ...形式传入
MGET user:name user:email	# 获取多个数据，返回数组，不存在的值，返回nil，程序中一般表示为NULL
SETNX key value				# SETNX用于原子性的判断键不存在才写入值返回值：1，存在则不写入，返回值：0
INCR views					# 键值自增，步长为1，命令意义为：执行自增并返回自增后的值
DECR views					# 与INCR相反
INCRBY views 10				# 自增，自定义步长
DECRBY views 5				# 自减，自定义步长

[LIST]
LPUSH key v1 v2 v3 ...		# 添加多个元素到List数据结构中(从列表左边，即队首加入元素)
LLEN key					# 返回列表的长度
LPOP key					# 从列表中弹出一个元素(取出并从列表中删除)，从列表左边，即队首弹出元素
RPUSH key v1 v2 v3 ...		# 与LPUSH意义相同，方向相反
RPOP key					# 与LPOP意义相同，方向相反
LRANGE key 1 -3				# 取出队列元素，正数表示从边索引取(0表示第一个)、负数表示从右边索引取(-1表示右起第一个)

[SET]
SADD key v1 v2 v3 v1 ...	# 向SET中添加元素，重复添加将被忽略，返回添加成功的个数
SMEMBERS key				# 获取SET中所有元素，SET类型不支持部分获取
SPOP key					# 随机弹出一个元素
SISMEMBER key value			# 判断集合中是否存在指定元素
SCARD key					# 返回SET元素个数
SREM key value				# 删除指定value

[HASH]
HSET key field value		# 添加一个键值对到Hash结构
HMSET key f1 v1 f2 v2 ...	# 添加多个键值对
HGET key field				# 取出一个字段对应值
HMGET key f1 f2 f3 ...		# 取出多个字段对应值
HGETALL key					# 取出全部字段，返回列表，以field/value/field/value ... 形式
HLEN key					# 查询字段数量
HKEYS key					# 取出全部字段名
HVALS key					# 取出全部值
HEXISTS key field			# 判断指定字段是否存在
HDEL key field				# 删除指定字段
HINCRBY	key field step		# 字段自增，step是自增长度

[ZSET]
ZADD user:score 98 Lucy 62 Lily 75 Jack	# 添加元素到有序集合
ZRANGE user:score 0 -1		# 取出部分或全部元素(默认按分子从小到大排序)


```
