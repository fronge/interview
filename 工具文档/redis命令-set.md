命令|含义|
| -| -
SADD |	向集合添加一个或多个成员
SCARD |	获取集合的成员数
SDIFF |	返回给定所有集合的差集
SDIFFSTORE |	返回给定所有集合的差集并存储在 destination 中
SINTER |	返回给定所有集合的交集
SINTERSTORE |	返回给定所有集合的交集并存储在 destination 中
SISMEMBER |	判断 member 元素是否是集合 key 的成员
SMEMBERS |	返回集合中的所有成员
SMOVE |	将 member 元素从 source 集合移动到 destination 集合
SPOP |	移除并返回集合中的一个随机元素
SRANDMEMBER |	返回集合中一个或多个随机数
SREM |	移除集合中一个或多个成员
SUNION |	返回所有给定集合的并集
SUNIONSTORE |	所有给定集合的并集存储在 destination 集合中
SSCAN |	迭代集合中的元素