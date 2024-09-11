# Sentinel
## 概览
- Sentinel（哨岗、哨兵）是Redis的高可用性（high availability）解决方案：由一个或多个Sentinel实例（instance）组成的Sentinel系统（system）可以监视任意多个主服务器，以及这些主服务器属下的所有从服务器，并在被监视的主服务器进入下线状态时，自动将下线主服务器属下的某个从服务器升级为新的主服务器，然后由新的主服务器代替已下线的主服务器继续处理命令请求。
## 过程
- 当主服务器下线时长超过用户设定的下线时长上限时，Sentinel会开始发起故障迁移操作
- 首先，Sentinel系统会挑选主服务器属下的其中一个从服务器，并将这个被选中的从服务器升级为新的主服务器
- 之后，Sentinel系统会向原主服务器属下的所有从服务器发送新的复制指令，将它们重新配置到新的主服务器上
- 另外，Sentinel还会继续监视已下线的原主服务器，并在它重新上线时，将它设置为新的主服务器的从服务器

## 启动并初始化Sentinel
```
$ redis-sentinel /path/to/your/sentinel.conf
```
### 执行命令的步骤
- 初始化服务器
  - Sentinel本质上只是一个运行在特殊模式下的Redis服务器，所以大部分操作和初始化一个普通redis服务器时类似。Sentinel执行的工作和普通Redis服务器执行的工作不同，所以会有一些特殊的操作。
- 将普通Redis服务器使用的代码替换成Sentinel专用代码
  - 将一部分普通Redis服务器使用的代码替换成Sentinel专用代码
- 初始化Sentinel状态
- 根据给定的配置文件，初始化Sentinel的监视主服务器列表
  - 初始化Sentinel状态的masters属性
    - 字典的键是被监视主服务器的名字
    - 字典的值则是被监视主服务器对应的sentinel.c/sentinelRedisInstance结构
- 创建连向主服务器的网络连接
  - Sentinel将成为主服务器的客户端，它可以向主服务器发送命令，并从命令回复中获取相关的信息
  - 建立两个链接
    - 一个是命令连接，这个连接专门用于向主服务器发送命令，并接收命令回复
    - 另一个是订阅连接，这个连接专门用于订阅主服务器的__sentinel__:hello频道

## 获取主服务器信息
- Sentinel默认会以每十秒一次的频率，通过命令连接向被监视的主服务器发送INFO命令，并通过分析INFO命令的回复来获取主服务器的当前信息
### 获取的内容
- 关于主服务器本身的信息，包括run_id域记录的服务器运行ID，以及role域记录的服务器角色
  - 根据run_id域和role域记录的信息，Sentinel将对主服务器的实例结构进行更新
- 另一方面是关于主服务器属下所有从服务器的信息
  - 根据这些IP地址和端口号，Sentinel无须用户提供从服务器的地址信息，就可以自动发现从服务器
## 获取从服务器信息
- 当Sentinel发现主服务器有新的从服务器出现时，Sentinel除了会为这个新的从服务器创建相应的实例结构之外，Sentinel还会创建连接到从服务器的命令连接和订阅连接
- 每十秒一次的频率通过命令连接向从服务器发送INFO命令

### 获取的内容
- 从服务器的运行ID run_id
- 从服务器的角色role
- 主服务器的IP地址master_host，以及主服务器的端口号master_port
- 主从服务器的连接状态master_link_status 
- 从服务器的优先级slave_priority
- 从服务器的复制偏移量slave_repl_offset
## 向主服务器和从服务器发送信息
- 在默认情况下，Sentinel会以每两秒一次的频率，通过命令连接向所有被监视的主服务器和从服务器发送以下格式的命令
  - 以s_开头的参数记录的是Sentinel本身的信息
  - m_开头的参数记录的则是主服务器的信息
```
PUBLISH __sentinel__:hello "＜s_ip＞,＜s_port＞,＜s_runid＞,＜s_epoch＞,＜m_name＞,＜m_ip＞,＜m_port＞,＜m_epoch＞"
```
## 接收来自主服务器和从服务器的频道信息
- 当Sentinel与一个主服务器或者从服务器建立起订阅连接之后，Sentinel就会通过订阅连接，向服务器发送以下命令,一直持续到Sentinel与服务器的连接断开为止
```
SUBSCRIBE __sentinel__:hello
```
- 对于监视同一个服务器的多个Sentinel来说，一个Sentinel发送的信息会被其他Sentinel接收到，这些信息会被用于更新其他Sentinel对发送信息Sentinel的认知，也会被用于更新其他Sentinel对被监视服务器的认知
- Sentinel从__sentinel__:hello频道收到一条信息时，会对这条信息进行分析，提取出信息中的Sentinel IP地址、Sentinel端口号、Sentinel运行ID等八个参数，并进行以下检查
  - 如果信息中记录的Sentinel运行ID和接收信息的Sentinel的运行ID相同, 不做进一步处理
  - 如果信息中记录的Sentinel运行ID和接收信息的Sentinel的运行ID不相同，说明这条信息是监视同一个服务器的其他Sentinel发来的，Sentinel将根据信息中的参数，对相应主服务器的实例结构进行更新

### 更新sentinels字典
- sentinels字典的键是其中一个Sentinel的名字，格式为ip:port，比如对于IP地址为127.0.0.1，端口号为26379的Sentinel来说，这个Sentinel在sentinels字典中的键就是"127.0.0.1:26379"
- sentinels字典的值则是键所对应Sentinel的实例结构，比如对于键"127.0.0.1:26379"来说，这个键在sentinels字典中的值就是IP为127.0.0.1，端口号为26379的Sentinel的实例结构
- 当一个Sentinel接收到其他Sentinel发来的信息时，会从信息中分析并提取出以下两方面参数
  - 与Sentinel有关的参数：源Sentinel的IP地址、端口号、运行ID和配置纪元
  - 与主服务器有关的参数：源Sentinel正在监视的主服务器的名字、IP地址、端口号和配置纪元
- 因为一个Sentinel可以通过分析接收到的频道信息来获知其他Sentinel的存在，并通过发送频道信息来让其他Sentinel知道自己的存在，所以用户在使用Sentinel的时候并不需要提供各个Sentinel的地址信息，监视同一个主服务器的多个Sentinel可以自动发现对方

### 创建连向其他Sentinel的命令连接
- 监视同一个主服务器的多个Sentinel可以自动发现对方