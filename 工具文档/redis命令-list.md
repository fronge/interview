# 列表操作
|命令|含义|
|-|-|
LSET | 通过索引设置列表元素的值
LPUSH |	将一个或多个值插入到列表头部
LPUSHX | 将一个值插入到已存在的列表头部
RPUSH |	在列表中添加一个或多个值
LPOP |	移出并获取列表的第一个元素
RPOP |	移除并获取列表最后一个元素
BLPOP |	移出并获取列表的第一个元素
BRPOP |	移出并获取列表的最后一个元素
RPOPLPUSH |	移除列表的最后一个元素，并将该元素添加到另一个列表并返回
BRPOPLPUSH | 从列表中弹出一个值，并将该值插入到另外一个列表中并返回它
LINDEX | 通过索引获取列表中的元素
LINSERT | 在列表的元素前或者后插入元素
LLEN | 获取列表长度
LRANGE | 获取列表指定范围内的元素
LREM |	移除列表元素
LTRIM |	对一个列表进行修剪(trim)
RPUSHX | 为已存在的列表添加值

# 实际例子
```bash
127.0.0.1:6379> RPUSH mylist a b c d e f g h i j
(integer) 10
127.0.0.1:6379> LRANGE mylist 0 10
1) "a"
2) "b"
3) "c"
4) "d"
5) "e"
6) "f"
7) "g"
8) "h"
9) "i"
10) "j"
127.0.0.1:6379> LTRIM mylist 0 1
OK
127.0.0.1:6379> LRANGE mylist 0 10
1) "a"
2) "b"
127.0.0.1:6379> LTRIM mylist -5 5
OK
127.0.0.1:6379> LRANGE mylist 0 10
1) "a"
2) "b"
127.0.0.1:6379> RPUSH mylist one
(integer) 3
```