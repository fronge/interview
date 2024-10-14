# 管理命令
命令|含义|
| -| -
BGREWRITEAOF | 	异步执行一个 AOF（AppendOnly File） 文件重写操作
BGSAVE	 | 在后台异步保存当前数据库的数据到磁盘
CLIENT	 | 关闭客户端连接
CLIENT LIST	 | 获取连接到服务器的客户端连接列表
CLIENT GETNAME	 | 获取连接的名称
CLIENT PAUSE	 | 在指定时间内终止运行来自客户端的命令
CLIENT SETNAME	 | 设置当前连接的名称
CLUSTER SLOTS	 | 获取集群节点的映射数组
COMMAND	 | 获取 Redis 命令详情数组
COMMAND COUNT	 | 获取 Redis 命令总数
COMMAND GETKEYS	 | 获取给定命令的所有键
TIME	 | 返回当前服务器时间
COMMAND INFO	 | 获取指定 Redis 命令描述的数组
CONFIG GET	 | 获取指定配置参数的值
CONFIG REWRITE	 | 修改 redis.conf 配置文件
CONFIG SET	 | 修改 redis 配置参数，无需重启
CONFIG RESETSTAT	 | 重置 INFO 命令中的某些统计数据
DBSIZE	 | 返回当前数据库的 key 的数量
DEBUG OBJECT	 | 获取 key 的调试信息
DEBUG SEGFAULT	 | 让 Redis 服务崩溃
FLUSHALL	 | 删除所有数据库的所有 key
FLUSHDB	 | 删除当前数据库的所有 key
INFO	 | 获取 Redis 服务器的各种信息和统计数值
LASTSAVE	 | 返回最近一次 Redis 成功将数据保存到磁盘上的时间
MONITOR	 | 实时打印出 Redis 服务器接收到的命令，调试用
ROLE	 | 返回主从实例所属的角色
SAVE	 | 异步保存数据到硬盘
SHUTDOWN	 | 异步保存数据到硬盘，并关闭服务器
SLAVEOF	 | 将当前服务器转变从属服务器(slave server)
SLOWLOG	 | 管理 redis 的慢日志
SYNC	 | 用于复制功能 ( replication ) 的内部命令

# 发布订阅
命令|含义|
| -| -
PSUBSCRIBE |	订阅一个或多个符合给定模式的频道。
PUBSUB |	查看订阅与发布系统状态。
PUBLISH |	将信息发送到指定的频道。
PUNSUBSCRIBE |	退订所有给定模式的频道。
SUBSCRIBE |	订阅给定的一个或多个频道的信息。
UNSUBSCRIBE |	指退订给定的频道。

# 事务
命令|含义|
| -| -
DISCARD |	取消事务，放弃执行事务块内的所有命令
EXEC |	执行所有事务块内的命令
MULTI |	标记一个事务块的开始
UNWATCH |	取消 WATCH 命令对所有 key 的监视
WATCH |	监视一个(或多个) key

# 连接
命令|含义|
| -| -
AUTH password |	验证密码是否正确
ECHO message |	打印字符串
PING	 |查看服务是否运行
QUIT	 |关闭当前连接
SELECT index |	切换到指定的数据库


# 脚本相关
命令|含义|
| -| -
SCRIPT KILL | 	杀死当前正在运行的 Lua 脚本。
SCRIPT LOAD	 | 将脚本 script 添加到脚本缓存中，但并不立即执行这个脚本。
EVAL	 | 执行 Lua 脚本。
EVALSHA	 | 执行 Lua 脚本。
SCRIPT EXISTS | 	查看指定的脚本是否已经被保存在缓存当中。
SCRIPT FLUSH | 从脚本缓存中移除所有脚本。

# HyperLogLog
命令|含义|
| -| -
PFGMERGE	|将多个 HyperLogLog 合并为一个 HyperLogLog
PFADD	|添加指定元素到 HyperLogLog 中。
PFCOUNT	|返回给定 HyperLogLog 的基数估算值。

# 地理位置(geo) 命令
命令|含义|
| -| -
GEOHASH |	返回一个或多个位置元素的 Geohash 表示
GEOPOS |	从key里返回所有给定位置元素的位置（经度和纬度）
GEODIST |	返回两个给定位置之间的距离
GEORADIUS |	以给定的经纬度为中心， 找出某一半径内的元素
GEOADD |	将指定的地理空间位置（纬度、经度、名称）添加到指定的key中
GEORADIUSBYMEMBER |	找出位于指定范围内的元素，中心点是由给定的位置元素决定