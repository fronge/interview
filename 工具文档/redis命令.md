# 数据库操作
- 测试连接
```
ping
```

选择数据库
```
<!-- SELECT db_index -->
SELECT 1
```
查看数据库信息
```
info
```

查看当前选择的数据库使用大小
```
<!-- DBSIZE -->
DBSIZE
```

清空数据库
  - 慎用
```
flushdb
```
清空所有数据库
  - 慎用
```
flashall
```

退出连接
```
quit 或者 exit
```

## key 操作
删除key
```
DEL key1 [key2 .. key_N]
```
查看key类型
```
TYPE key
```


序列化给定 key ，并返回被序列化的值
```
DUMP key
```

检查给定 key 是否存在
```
EXISTS key
```

为给定 key 设置过期时间
```
EXPIRE key seconds
```

用于为 key 设置过期时间，接受的时间参数是 UNIX 时间戳
```
EXPIREAT key timestamp-seconds
```

设置 key 的过期时间，以毫秒计
```
PEXPIRE key ttl-milliseconds
```

设置 key 过期时间的时间戳(unix timestamp)，以毫秒计
```
PEXPIREAT key timestamp-ms
```

查找所有符合给定模式的 key
  - patern 支持 * ? [a-z]
```
KEYS patern
```
将当前数据库的 key 移动到给定的数据库中
 ```
MOVE key db
```

移除 key 的过期时间，key 将持久保持
```
PERSIST key
```

以毫秒为单位返回 key 的剩余的过期时间
```
PTTL key
```

以秒为单位，返回给定 key 的剩余生存时间
```
TTL key
```

从当前数据库中随机返回一个 key
```
RANDOMKEY
```

修改 key 的名称
```
RENAME key newkey
```

仅当 newkey 不存在时，将 key 改名为 newkey
```
RENAMENX key newkey
```



# 字符串
## 设置值 
- 普通设置值
  - NX: 只有在 key 不存在时，才设置 key
  - XX: 只有在 key 存在时，才设置 key
  - timestamp: 过期时间，单位秒
  - 成功返回OK，否则返回nil
```
SET key value NX|XX
```
- 设置值并返回旧值
  - 成功返回旧值，否则返回nil
```
GETSET key value
```

- 批量设置值
  - 成功返回1，否则返回0
```
MSET key1 value1 ... keyN valueN
```
- 批量设置值，只会在所有给定键都不存在的情况下对键进行设置
  - 成功返回1，否则返回0
```
MSETNX key value [key value ...]
```
- 自增(整数类型)，如果key不存在，那么在执行incr操作之前，服务器会自动将key的值设为0
```
INCR key
```

- 自增(整数类型),指定步长
```
INCRBY key increment
<!-- 年龄加2 -->
INCRBY age 2
<!-- 年龄减2 -->
INCRBY age -2
```

- 自减1(整数类型)
```
DECR
```

- 浮点数自增指定步长
  - 必须指定步长
```
<!-- INCRBYFLOAT key increment -->
INCRBYFLOAT k 1.2
```
  
## 获取
- 普通获取
```
GET key
 ```
 - 批量获取
   - 成功返回所有 key 对应的 value，否则返回nil
```
MGET key1 ... keyN
```
- 获取指定范围内的字符串
  - 成功返回指定范围的字符串，否则返回空字符串
```
GETRANGE key start end
```
- 获取字符串长度
```
STRLEN key
```
- 获取字符串值指定索引范围上的内容
```
GETRANGE key start end
```
- 对字符串值的指定索引范围进行设置
  - 给定的index索引超出字符串值的长度时，字符串值末尾直到索引index-1之间的部分将使用空字节进行填充
```
SETRANGE key index substitute
```
```
redis> GET message
"hello world"

redis> SETRANGE message 6 "Redis"
(integer) 11     -- 字符串长度为11字节

redis> GET message
"hello Redis"
```