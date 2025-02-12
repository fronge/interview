# 网络相关
## 常见的状态码
- 100 临时响应
  - 100 继续，请求者应当继续提出请求
  - 101 切换协议
- 200 成功响应
  - 200: 服务器成功处理请求
  - 201 以创建，请求成功并且服务器创建了新的资源
  - 202 已接受：服务器已接受请求，但尚未处理
  - 203 非授权信息：服务器已成功处理请求，但返回的信息可能来自另一来源
  - 204 无内容：服务器成功处理了请求，但没有返回任何内容
  - 205 重置内容： 服务器成功处理请求，没有返回任何内容，此响应要求请求者重置文档视图（例如: 清除表单内容以输入新内容）
  - 206 部分内容 服务器已成功处理部分GET请求
- 300 重定向
  - 300 多种选择： 针对请求，服务器可执行多种操作，服务器可根据请求者选择一项操作，提供操作列表共请求者选择
  - 301 永久移动： 请求页面永久移动刀新的位置
  - 302 临时移动： 服务器目前从不通位置的网页响应请求，但请求者应继续使用原有位置来响应以后的请求
  - 303 查看其他位置： 请求者应当对不同的位置使用单独的GET请求来检索响应时，服务器返回此代码
  - 304 为修改：自上次请求后，页面未修改。不返回网页内容
  - 305 使用代理：请求者只能使用代理访问请求的网页
  - 307 临时重定向 服务器目前从不同位置的网页相应请求，请求者应继续使用原有位置来响应以后的请求。
- 400 请求错误
  - 400 请求错误： 服务器不理解请求的语法
  - 401 未授权：要求身份验证
  - 403 禁止访问： 服务器拒绝请求
  - 404 未找到： 服务器找不到请求的页面
  - 405 方法禁止： 禁用请求中指定的方法
  - 406 不接受： 无法使用请求的内容特性响应请求网页
  - 407 需要代理授权
  - 408 请求超时
  - 409 冲突
  - 410 已删除： 请求资源永久删除
  - 411 需要有效长度
  - 412 为满足提前条件
  - 413 请求实体过大
  - 414 请求URI过长
  - 415 不支持的媒体类型
  - 416 请求范围不符合要求
  - 417 维曼姿期望值： 服务器为满足“期望”请求表头字段的请求
- 500 服务器内部错误
  - 500 服务器内部错误，无法完成请求
  - 501 尚未实施 服务器不具备办成请求的功能
  - 502 错误网关：服务器作为网关或代理，从上游服务器收到无效响应
  - 503 服务不可用： 服务器目前无法使用（由于超早或者停机维护），通常只是暂时状态
  - 504 网关超时： 服务器作为网关或者代理，但是没后及时从上游服务器收到请求
  - 505 HTTP版本不受支持： 服务器不支持请求中所用的HTTP协议版本
## http 与 https 
- 答: Http协议运行在TCP之上，明文传输，客户端与服务器端都无法验证对方的身份；Https是身披SSL(Secure Socket Layer)外壳的Http，运行于SSL上，SSL运行于TCP之上，是添加了加密和认证机制的HTTP。二者之间存在如下不同：
  - 端口不同：Http与Http使用不同的连接方式，用的端口也不一样，前者是80，后者是443；
  - 资源消耗：和HTTP通信相比，Https通信会由于加减密处理消耗更多的CPU和内存资源；
  - 开销：Https通信需要证书，而证书一般需要向认证机构购买；
- Https的加密机制是一种共享密钥加密和公开密钥加密并用的混合加密机制。

## 对称加密与非对称加密
- 对称密钥加密是指加密和解密使用同一个密钥的方式，这种方式存在的最大问题就是密钥发送问题，即如何安全地将密钥发给对方；而非对称加密是指使用一对非对称密钥，即公钥和私钥，公钥可以随意发布，但私钥只有自己知道。发送密文的一方使用对方的公钥进行加密处理，对方接收到加密信息后，使用自己的私钥进行解密。

## 简述http协议
- HTTP协议：超文本传输协议（HyperText Transfer Protocol)是一个应用层协议，由请求和响应构成，是一个标准的客户端/服务器模型。HTTP是一个无状态的协议，基于传输层的TCP协议

## 三次握手与四次挥手？
### (1)三次握手
- 第一次握手：Client将标志位SYN置为1，随机产生一个值seq=J，并将该数据包发送给Server，Client进入SYN_SENT状态，等待Server确认。
- 第二次握手：Server收到数据包后由标志位SYN=1知道Client请求建立连接，Server将标志位SYN和ACK都置为1，ack=J+1，随机产生一个值seq=K，并将该数据包发送给Client以确认连接请求，Server进入SYN_RCVD状态。
- 第三次握手：Client收到确认后，检查ack是否为J+1，ACK是否为1，如果正确则将标志位ACK置为1，ack=K+1，并将该数据包发送给Server，Server检查ack是否为K+1，ACK是否为1，如果正确则连接建立成功，Client和Server进入ESTABLISHED状态，完成三次握手，随后Client与Server之间可以开始传输数据了
### (2) 四次挥手
- 第一次挥手：Client发送一个FIN，用来关闭Client到Server的数据传送，Client进入FIN_WAIT_1状态。
- 第二次挥手：Server收到FIN后，发送一个ACK给Client，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号），Server进入CLOSE_WAIT状态。此时TCP链接处于半关闭状态，即客户端已经没有要发送的数据了，但服务端若发送数据，则客户端仍要接收。
- 第三次挥手：Server发送一个FIN，用来关闭Server到Client的数据传送，Server进入LAST_ACK状态。
- 第四次挥手：Client收到FIN后，Client进入TIME_WAIT状态，接着发送一个ACK给Server，确认序号为收到序号+1，Server进入CLOSED状态，Client端等待2倍MSL时间，自动关闭，完成四次挥手。

## 网页输入网址发生了么?
- 1: 查找ip: 当输入一个域名的时候，浏览器会先查看hosts文件是否有相应的配置，如果有，则直接访问配置的ip，如果没有，则会查看一下浏览器的缓存，如果缓存也没有，则去DNS服务器解析域名，获取相应的ip地址。
- 2:建立TCP连接: 三次握手建立TCP链接
- 3:请求数据: 建立连接后，根据端口映射，一般会经过nginx等负载均衡服务器做映射，找到相应http服务，根据路由规则映射到相应的具体服务，服务器处理完请求后，会生成一个http response，根据之前的网络传输返回给前端
- 4:接收和显示: 前端会根据这个响应做出相应的页面渲染，将数据页面展示在浏览器上。

## HTTP2 多路复用？
- 1、HTTP/1 中，每次请求都要建立一次HTTP链接，消耗大量的资源，开启了Keep-Alive,可以解决多次链接的问题，但是，还存在两个问题:
  - 1、串行的文件传输
  - 2、连接数过多
- 2、HTTP2多路复用就是解决这两个问题的，帧（frame）和流（stream）
帧代表着最小的数据单位，每个帧会标识出该帧属于哪个流，流也就是多个帧组成的数据流。
多路复用，就是在一个 TCP 连接中可以存在多条流。换句话说，也就是可以发送多个请求，对端可以通过帧中的标识知道属于哪个请求。通过这个技术，可以避免 HTTP 旧版本中的队头阻塞问题，极大的提高传输性能。

## 七层网络协议、五层网络协议？
- (1)、七层网络协议
- 物理层、数据链路层、网络层主要负责数据通信
- 会话层、表示层、应用层主要负责数据处理
- 传输层负责通信子网与资源子网的接口和桥梁，承上启下
  - 物理层
    - 以0、1代表电压的高低、灯光的闪灭。界定连接器与网线的规格
  - 数据链路层
    - 互联设备之间传送和识别数据帧
    - 目的: 采用差错检查、差错控制、流量控制等方法，将有差错的物理线路变成无差错的数据链路。
    - 传输单位为帧
      - 成帧: 从物理层的比特流提取出完整的帧
    - MAC地址
    
  - 网络层
    - 地址管理与路由选择
    - 作用: 管理网络中的数据通信，两个端系统之间的数据透明传送
    - 功能
      - 分组与分组交换: 把传输层接收到的数据保温封装成组(packet，包)向下传输到数据链路层
      - 路由: 通过路由算法为分组通过通信子网选择适当的路径
      - 网络链接复用: 为分组在通信子网中节点之间的传输创建逻辑链路，在一题哦啊数据链路上服用多条网络链接(多采用时分复用技术)
      - 差错检测与恢复: 一般用分组中的头部校验和进行差错校验，使用确认和重传机制进行差错恢
      - 服务选择：可为传输层提供 数据报和虚电路两种服务，iternet网络层只提供数据报一种服务
      - 网络管理: 管理网络中的数据通信过程,为传输层提供最基本的端到端的数据传送服务
      - 流量控制: 通过流量整形技术实现流量控制防止通信量过大造成通信子网的性能下降
      - 拥塞控制：当网络数据量过大，造成网络拥塞，致使网络吞吐能力下降，需采用适当的控制措施来进行疏导
      - 网络互连: 把一个网与另一个网络连接起来，实现用户之间的跨网络通信
      - 分片与重组: 如果发送的分组(包)太大，超过协议数据但愿允许的长度，源节点的网络层对该分组进行分片，分片到达目的主机后，由目的节点的网络层重新组装成原分组
    - IP协议
      - IP编址方案、分组封装格式、分组转发规则 
     
  - 传输层
    - 管理两个节点之间的数据传输、负责可靠、有效的报文传输服务
    - 在终端用户之间提供透明的数据传输，向上层提供可靠的数据传输服务
    - 服务阶段
      - 传输连接建立阶段
      - 数据传输阶段
      - 传输连接释放阶段
    - 提供服务
      - 逻辑连接的建立
      - 传输层寻址
      - 数据传输
      - 传输连接释放
      - 流量控制
      - 拥塞控制
      - 多路复用和解复用
      - 崩溃恢复
    - 信息传送的协议数据单元
      - 段或者报文 
    - TCP/UDP协议
      - TCP:传输控制协议
      - UDP:用户数据报协议

  - 会话层
    - 通信管理。负责件礼盒新开通信链接(数据流动的逻辑通路)。管理传输层以下的分层
    - 功能
      - 会话管理
      - 数据流同步
      - 重新同步

  - 表示层
    - 设备固有数据格式和网络标准数据格式的转换
      - 将应用处理的信息转换为网络标准传输格式
      - 为异种机通信提供一种公共语言，以便能进行互操作
    - 功能
      - 语法转换: 代码转换及字符集的转换，数据格式操作的适配、数据压缩、数据加密等
      - 语法选择: 提供出事选择的一种语法和随后修改这种选择的手段
      - 联接管理：利用会话层提供的服务建立表示联接，管理这一联接之上的数据传输和同步控制，以及正常或者非正常地种植联接
  - 应用层
    - 针对特定应用的协议
      - 直接为用户应用的进程提供服务，规定应用程序通信相关的细节
        - HTTP
          - 超文本传输协议， 网络访问协议 
        - SMTP
          - 邮件协议 
        - FTP
          - 文件传输协议 
        - DNS
          - 域名解析服务 
        - POP3
          - 邮件读取协议 
        - SNMP
          - 邮件传输协议 面向链接 
        - Telnet
          - 远程登录协议
        - SSH协议

## TCP/IP协议?
### 五层-七层映射
- 应用层
  - 应用层
  - 表示层
  - 会话层
  - 作用:
    - 根据HTTP协议组装数据包, GET /HTTP/1.1
- 传输层
  - 传输层
  - 作用
    - 根据TCP建立安全的稳定的连接
    - 添加TCP头：端口号、序列号等
- 网络互联层/IP
  - 网络层
  - 作用:
    - 添加IP头部、源IP地址等
- 网络接口层
  - 数据链路层
  - 作用:
    - 添加以太网头部、MAC地址等 
- 物理层
  - 物理层
  - 作用:
    - 传递数据 
### TCP三次握手&四次挥手
- 为什么是三次?
  - 三次分别确认了client、server 端的发送和接收能力
  - 双方检查和确认自己的网络状态，确保连接的可靠性

- 结束为什么是四次
  - 确保了双方都明确地表示愿意关闭连接
  - 都已准备好停止数据交换，从而避免了数据丢失或连接错误
  - 双方检查和确认自己的网络状态，确保连接的安全关闭
  - 保持TCP状态信息的一致性

### UDP协议
####  特点
  - 无连接：UDP不需要像TCP那样的握手和断开连接的过程，数据可以直接发送。
  - 低延迟：由于没有建立和维护连接的开销，UDP通常比TCP有更低的延迟。
  - 不可靠：UDP不保证数据包的顺序、完整性或者可靠性。发送的数据包可能会丢失、重复或者乱序到达。
  - 灵活性：UDP的简单性使得它非常适合于那些对时间敏感且可以容忍一定数据丢失的应用，如视频播放、在线游戏等。
### IP协议
- 互联网协议 (IP) 是一种协议或一组规则，用于对数据包进行路由和寻址，以便它们可以跨网络传输并到达正确的目的地。互联网上传输的数据会被切分为较小的碎片，称之为数据包。每个数据包上会附加相应的 IP 信息，它有助于路由器将数据包发送到正确的位置。每个已连接互联网的设备或域会获得一个分配的 IP 地址，并且当数据包定向到与之关联的 IP 地址时，数据便能够到达预期的位置。