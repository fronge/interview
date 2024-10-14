# 命令表
命令|含义|
| -| -
HSET |	用于设置存储在 key 中的哈希表字段的值
HGET |	获取存储在哈希表中指定字段的值
HGETALL |	获取在哈希表中指定 key 的所有字段和值
HKEYS |	获取存储在 key 中的哈希表的所有字段
HVALS |	用于获取哈希表中的所有值
HLEN |	获取存储在 key 中的哈希表的字段数量
HEXISTS |	用于判断哈希表中字段是否存在
HINCRBY |	为存储在 key 中的哈希表指定字段做整数增量运算
HDEL |	用于删除哈希表中一个或多个字段

# 实操命令展示
- 设置: HSET
  - 设置一个或多个字段为对应值
```bash
> HSET h name zhangsan age 18 address beijing
(integer) 3
```
- 获取: HGET
  - 返回对应的值
```bash
> HGET h name
"zhangsan"
> HGET h age
"18"
```
- 获取所有: HGETALL
  - 返回所有字段及对应的值
```bash
> HGETALL h
1) "name"
2) "zhangsan"
3) "age"
4) "18"
5) "address"
6) "beijing"
```
- 获取字段: HKEYS
  - 返回所有字段名称
```bash
> HKEYS h
1) "name"
2) "age"
3) "address"
```
- 获取所有值: HVALS
  - 返回所有字段的值
```bash
> HVALS h
1) "zhangsan"
2) "18"
3) "beijing"
```
- 获取长度: HLEN
  - 返回哈希表中的字段数量
```bash
> HLEN h
(integer) 3
```
- 判断字段是否存在: HEXISTS
  - 存在返回1, 不存在返回0
```bash
> HEXISTS h name
(integer) 1

> HEXISTS h hometawn
(integer) 0
```
- 为字段加值: HINCRBY
```bash
> HINCRBY h age 2
(integer) 20
> HGET h age
"20"
```
- 删除字段: HDEL
```bash
> HGET h address
 "beijing"
> HDEL h address
(integer) 1
> HGET h address
(nil)
```