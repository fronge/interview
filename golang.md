# *golang相关面试问题及知识点*
### golang 对比Python，区别在哪?
- 这个是一个发挥题，可以根据自己的了解，做一些总结性对比
- 范例
  - Python 是一种面向对象编程的多范式, 命令式和函数式编程语言。
  - Go是一种基于并发编程范式的过程编程语言
- 类型
  - Python是动态类型解释型语言
  - Go是静态类型编译型语言
- 并发
  - Python的GIL锁的存在,, 基于相关的库完成
  Go有内置的并发机制
- 使用方向
  - python提供大量的类库，使用方便，在科学计算方面应用非常广泛
  - golang的第三方库、资源包也在逐渐增长，在高并发的场景下，golang的表现更加出色
- 语法
  - Python的语法可读性更高
  - Golang的代码的编写更加严格，变量声明必须使用，这就从语法层面减少了内存的浪费

### 介绍一下数组、切片，他们的关系及区别?
- 数组是一个存放一系列相同类型的数据结构，数组一经创建,长度，容量不可改变
- 切片是基于数组而实现的一种数据结构, 是对底层数组的抽象，内存连续分配，切片本身是一
个只读对象，其工作机制类似数组指针的一种封装,切片对象本身非常小,只有三个字段的数据结构：指向底层数组的指针，切片长度，切片容量.
- 区别:
  - 数组长度、容量一经确认，不可改变，切片长度、容量可以改变
  - 数组是值传递， 切片是引用传递 
### 切片扩容
- 切片扩容机制及注意事项
  - 首先判断,新容量未旧容量的2倍,直接为新容量.
  - 否则,旧切片长度小于1024，最终容积是旧容量的2倍
  - 否则,旧切片长度大于等于1024， 最终容量从就容量开始循环增加原来的1/4，直到最终容量大于等于新申请的容量。
  - 如果最终容量计算值溢出(超过int最大值)，则最终容量就是新申请的容量.
```
// 扩容代码
func growslice(et *_type, old slice, cap int) slice {
	// 将当前容量取出
	newcap := old.cap
	// 当前容量*2
	doublecap := newcap + newcap
	// cap:预估容量是否大于两倍的当前容量
	if cap > doublecap {
		// 新容量 = 预估容量
		newcap = cap
	} else {
		// 当前容量是否小于1024
		if old.cap < 1024 {
			// 两倍扩容
			newcap = doublecap
		} else {
			// Check 0 < newcap to detect overflow
			// and prevent an infinite loop.
			// 新容量 = 循环增加1.25倍，直至newcap > cap
			for 0 < newcap && newcap < cap {
				newcap += newcap / 4
			}
			// Set newcap to the requested cap when
			// the newcap calculation overflowed.
            // 如果经过循环1.25增加，最终newcap计算值溢出，即超过了int的最大范围，则最终容量就是新申请的容量
			if newcap <= 0 {
				newcap = cap
			}
		}
	}
	............
    // 内存分配相关
    ............
	// 将原数组复制到新地址
	memmove(p, old.array, lenmem)
	// 返回新创建的结构体
	return slice{p, old.len, newcap}
}

```
### 扩容前后的 Slice 是否相同？
  - 情况一: 原数组还有容量可以扩容（实际容量没有填充完），这种情况下，扩容以后的切片还是指向原来的数组，对一个切片操作可能影响多个指向相同地址的切片.
  - 情况二: 原来数组容量已经达到最大值，无法再扩容，Go会默认开出一片内存区,把原来的值拷贝出来，然后再执行append 操作。这样，原数组不受任何影响。
  - 如果要复制一个切片，最好使用 copy 函数。
  - 如果是函数操作切片，需要使用切片处理结果，则需要将处理后的切片作为参数返回

### map实现
- [Go 语言 map 的底层实现参考博客](https://zhuanlan.zhihu.com/p/406751292#:~:text=%E5%9C%A8%20go%20map%20%E4%B8%AD%2C%20%E9%92%88%E5%AF%B9%20go%20map%20%E7%9A%84%E7%89%B9%E5%AE%9A%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%2C,%E5%9B%A0%E6%AD%A4%E6%97%B6%E8%A3%85%E8%BD%BD%E5%9B%A0%E5%AD%90%E5%B9%B6%E6%B2%A1%E6%9C%89%E8%B6%85%E8%BF%87%206.5%2C%20%E8%BF%99%E6%84%8F%E5%91%B3%E7%9D%80%20map%20%E4%B8%AD%E7%9A%84%E5%85%83%E7%B4%A0%E6%95%B0%E7%9B%AE%E5%B9%B6%E4%B8%8D%E6%98%AF%E5%BE%88%E5%A4%9A%2C%20%E5%9B%A0%E6%AD%A4%E8%BF%99%E6%97%B6%E7%9A%84%E6%89%A9%E5%AE%B9%E7%AD%96%E7%95%A5%E6%98%AF%E7%AD%89%E9%87%8F%E6%89%A9%E5%AE%B9%2C%20%E5%8D%B3%E6%96%B0%E5%BB%BA%E5%AE%8C%E5%85%A8%E7%AD%89%E9%87%8F%E7%9A%84%E5%93%88%E5%B8%8C%E6%A1%B6%2C%20%E7%84%B6%E5%90%8E%E5%B0%86%E5%8E%9F%E5%93%88%E5%B8%8C%E6%A1%B6%E7%9A%84%E6%89%80%E6%9C%89%E5%85%83%E7%B4%A0%E6%90%AC%E8%BF%81%E5%88%B0%E6%96%B0%E7%9A%84%E5%93%88%E5%B8%8C%E6%A1%B6%E4%B8%AD)
- 底层实现
  - 底层实现是一个散列哈希表
  - key一定是可hash运算的, 当存入数据的时候，会先对key做hash运算，传统hash运算有两种方式，一种是取模法，一种是hash值与运算m-1进行与运算，确保每个桶都能算到，要保证桶的数量在 2的n次方。
  - golang采用的hash算法是与运算法。
  - 拉链法
    - 发生冲突，在做hash后，发现有数据，会在后面添加一个桶放数据，
  - 再这个散列表中主要出现的结构体有两个, hmap(header for a go map) 和 bmap(bucket for a go map)

```
 type hmap struct {
     count     int   // 键值对的数量
     flags     uint8
     B         uint8  // 桶的数目是2的多少次幂
     noverflow uint16 // 使用溢出桶的数量
     hash0     uint32
     
     buckets    unsafe.Pointer  // 桶在哪
     oldbuckets unsafe.Pointer  // 记录旧桶在哪
     nevacuate  uintptr         // 渐进式扩容下一个迁移的编号
     
     extra *mapextra  / 溢出桶相关
 }
 
 type bmap struct {
     
 }
 ```
### map扩容过程
- 双倍扩容、等量扩容
  - 双倍扩容判断依据:负载因子，数量/(2^B) > 6.5 
  - 等量扩容判断依据:负载因子没超标，noverflow较多，B<=15 noverflow>=2^B 或者 B>15 noverflow >= 2^15
- 双倍扩容
  - 多做插入操作，容量不够，会发生双倍扩容。扩容采取了一种称为“渐进式”的方式，原有的 key 并不会一次性搬迁完毕，每次最多只会搬迁 2 个 bucket。
- 等量扩容
  - 原因: 多做删除操作, 溢出桶增多，会发生等量扩容。
  - 作用: 重新排列。极端情况下，重新排列也解决不了，map 存储就会蜕变成链表，性能大大降低，此时哈希因子 hash0 的设置，可以降低此类极端场景的发生。
### map查找过程
- go语言中map采用的是哈希查找,由一个key通过哈希函数得到哈希值, 这个哈希值将key对应存到不同的桶里(bucket), 当有多个哈希映射到相同的桶里，使用链表解决哈希冲突。
  - key 经过 hash 后共 64 位，根据 hmap 中 B 的值，计算它到底要落在哪个
桶时，桶的数量为 2^B，如 B=5，那么用 64 位最后 5 位表示第几号桶，在用hash 值的高 8 位确定在 桶(bucket) 中的存储位置，当前 bmap 中的 bucket 未找到，则查询对应的 溢出桶(overflow bucket)，对应位置有数据则对比完整的哈希值，确定是否是要查找的数据。如果当前 map处于数据搬移状态，则优先从oldbuckets 中查找。

### sync.Map
- 一种线程安全的map

### 介绍一下channel
- Go 语言中，不要通过共享内存来通信，而要通过通信来实现内存共享。channel 收发遵循先进先出原则。分为缓冲区和无缓冲区，channel 中包括 buffer、sendx 和 recvx 收发的位置、sendq、recv。当channel因为缓冲区不足而阻塞了队列，则使用双向链表存储。

### channel收发特性
- 给一个nil channel 发送数据，永久阻塞
- 从一个nil channel 接收数据，永久阻塞
- 给一个已经关闭的channel发送数据，引发panic
- 从一个已经关闭的channel接收数据，缓冲为空，返回一个零值。
- 无缓冲的channel是同步，有缓冲的channel非同步。
- 关闭一个nil channel 会引发panic

### channel 的 环形缓冲区(ring buffer)
- channel 中使用了 ring buffer（环形缓冲区) 来缓存写入的数据。非常适用于FIFO固定长度队列。用 recvx 指向最早的被读取的数据，sendx指向再次写入时的位置。

- *写代码:交替打印问题*

### 锁的运行特性
- Mutex
```
type Mutex struct {
    state int32   // 锁状态
    sema  uint32  // 信号量
}  
```
  - lock、unlock通过atomic 库操作
  - 正常模式：所有等待锁的 goroutine 按照 FIFO（先进先出）顺序等待。唤醒的goroutine 不会直接拥有锁，而是会和新请求goroutine 竞争锁。新请求的goroutine更容易抢占：因为它正在 CPU 上执行，所以刚刚唤醒的goroutine有很大可能在锁竞争中失败。在这种情况下，这被唤醒的 goroutine 会加入到等待队列的前面。 
  - 饥饿模式：为了解决了等待goroutine队列的长尾问题，饥饿模式下，直接由unlock把锁交给等待队列中排在第一位的goroutine(队头)，同时，饥饿模式下，新进来的goroutine 不会参与抢锁也不会进入自旋状态，会直接进入等待队列的尾部。这样很好的解决了老的 goroutine 一直抢不到锁的场景。饥饿模式的触发条件：当一个goroutine 等待锁时间超过 1 毫秒时，或者当前队列只剩下一个 goroutine 的时候，Mutex切换到饥饿模式。
  - 两种模式，正常模式性能最好，饥饿模式解决了公平问题，这是性能和公平的平衡模式。

### Mutex允许自旋条件
- 锁已被占用，并且锁不处于饥饿模式。
- 积累的自旋数小于 4（最大自旋数）
- CPU核数大于1
- 有空闲的P
- 当前Goroutine所挂载的P下，本地待运行的队列为空
### RWMutex
- [读写锁相关博客](https://blog.csdn.net/LINZEYU666/article/details/123077252)
- 写锁需要阻塞写锁：一个协程拥有写锁时，其他协程写锁定需要阻塞
- 写锁需要阻塞读锁：一个协程拥有写锁时，其他协程读锁定需要阻塞
- 读锁需要阻塞写锁：一个协程拥有读锁时，其他协程写锁定需要阻塞
- 读锁不能阻塞读锁：一个协程拥有读锁时，其他协程也可以拥有读锁
```
type RWMutex struct {
    w           Mutex  // 控制多个写锁
    writerSem   uint32 // 写阻塞等待的信号量，最后一个读者释放锁的时候，释放信号量
    readerSem   uint32 // 读阻塞的协程等待的信号量, 持有写锁的协程释放锁后会释放信号量
    readerCount int32 // 记录读者数量
    readerWait  int32 // 记录写阻塞时读者数量
}

// 方法
RLock()   // 读锁定
RUnlock() // 解除读锁定
Lock()    // 写锁定，与Mutex完全一致
Unlock()  // 解除写锁定，与Mutex完全一致
```

### 结构体方法 指针与非指针区别
- 值接收只能在方法内部改变内部变量，内部生效
- 指针接收，改变变量全局生效
### 结构体内嵌，指针与非指针区别
- 正常使用没有区别
- 如果将结构体转换为接口，非指针继承会报错,指针继承可以直接转换
```
page main

"fmt"

type BaseInter interface {
	run()
	speak()
}
type Base struct{}

func (b *Base) run() {
	fmt.Println("Base run")
}
func (b *Base) speak() {
	fmt.Println("Base speak")
}

type OneSon struct {
	Base
}

type TwoSon struct {
	*Base
}

func main() {
	var i BaseInter
	s := TwoSon{
		&Base{},
	}
    // 没问题
	i = s
	i.run()

    s1 := OneSon{
		Base{},
	}
    // 报错 cannot use s (variable of type OneSon) as BaseInter value in assignment: missing method run (run has pointer receiver)
	i = s1
	i.run()
}
```
### context.Context包使用
- WithDealine
- WithTimeOut
- WithValue
- WithCancel
- *写代码:写一个方法用来做操作过期判断*

### golang并发模型
[Achieving concurrency in Go](https://medium.com/rungo/achieving-concurrency-in-go-3f84cbf870ca)
[go并发模型相关博客](https://www.jianshu.com/p/8ff2ac869f21)
- GPM调度模型

### Golang 的 GPM调度模型
- GM模型
  - G：gorutine， 存放在全局队列中
  - M: 线程，用来执行 gorutine
  - 调度过程: 每次M空闲需要执行 gorutine，就会从全局队列中取一个gorutine执行，执行完毕再放入队列或者销毁，每次，创建,获取,销毁 gorutine都会加锁，形成了激烈的锁竞争，严重影响执行效率。所以只用了4年，就优化成 GPM 模型
- GPM模型
  - 在GPM模型中，引进了 P:Processor 处理器，每个P中都包含一个G的队列，P的队列中最多存放256个gorutine，如果再多将会放在全局队列中。P的数量由启动配置参数 GOMAXPROCES确定
  - 调度过程: P在开始启动程序的时候创建，存放在数组中，P数量固定的，P会根据自己任务的情况创建M，P可以分配G, 如果M阻塞，P会寻找空闲的M，如果没有空闲的M，就会创建M，并与其绑定。
  - 调度器策略
    - 复用线程
      - work stealing机制
        - 当本地无可用的G，会尝试从其他的P的G队列中偷一半的G
      - hand off机制
        - 当本线程因为G进行系统调用阻塞时，线程释放绑定的P，把P转移给其他空闲的线程执行。
  - 全局队列
    - 在新的调度器中依然有全局G队列，但功能已经被弱化了，当M执行work stealing从其他P偷不到G时，它可以从全局G队列获取G。

### Golang内存分配规则
- Golang运行时的内存分配算法主要源自 Google 为 C 语言开发的 TCMalloc算法 ，全称 Thread-Caching Malloc 。
- 核心思想就是把内存分为多级管理，从而降低锁的粒度。 它将可用的堆内存采用二级分配的方式进行管理：每个线程都会自行维护一个独立的内存池，进行内存分配时优先从该内存池中分配，当内存池不足时才会向全局内存池申请，以避免不同线程对全局内存池的频繁竞争.

### 内存分配栈和堆？
- 栈:分配速度快，只需要CPU的两个指令“PUSH”和“RELEASE“进行分配和释放
- 堆: 分配速度较慢，首先需要找到一块大小合适的内存块，之后还需要gc垃圾回收才能释放。

### 内存逃逸
- 逃逸机制
  - 如果函数外部没有引用，优先放在栈上
  - 如果函数外部存在引用，必然放在堆上
  - 如果栈上放不下，必定放在堆上
- 逃逸分析
  - 逃逸分享就是由编译器决定那些变量放在栈上，那些放在堆上，通过编译参数-gcflags=-m 可以查看编译过程中的逃逸分析
- 逃逸场景
  - 指针逃逸
  - 栈空间不足
  - 变量大小不确定
  - 动态类型 // interface
  - 闭包引用对象
- 总结
  - 栈上分配的内存比在堆上分配的内存效率高
  - 栈上分配的内存不需要GC处理，堆上需要
  - 逃逸分析的目的决定内存分配地址是栈还是堆
  - 无论变量的大小，指针变量都会在堆上分配，对于小变量我们使用传值效率更高一些

### golang 垃圾回收机制
- 三色标记清除与混合写屏障
  - 三色标记
    - 白色标记表
      - 全部节点标记为白色 
    - 灰色标记表
      - 遍历根集合，将可达对象标记为灰色，遍历灰色的标记表，将可达的节点标记为灰色，全部检查完，将此节点放入黑色，继续遍历灰色，直到灰色没有对象。
    - 黑色标记表
      - 存放检查完成的节点，当灰色没有节点，清除白色的节点 
  - 三色标记存在的问题
    - 再GC的过程，灰色的节点于白色的节点断开引用，与此同时，黑色的节点与这个白色的节点建立引用关系。最后白色的节点无法被标记，被清理掉。
  - 强弱三色不变式
    - 强三色不变: 强制不允许黑色对象引用白色对象
    - 弱三色不变: 黑色对象可以引用白色对象，白色对象存在其他灰色对象的引用，可达他的链路上游存在灰色对象
  - 屏障机制
    - 插入写屏障
      - 对象被引用时触发: 在黑色对象引用白色对象时，B、白色对象被标记为灰色
      - 只在堆上使用，不在栈上使用
      - 在删除时，STW保护，重新扫描一下栈
      - 缺点：STW还是存在，影响
    - 删除屏障
      - 被删除对象，如果自身是灰色或者白色，那么被标记为灰色
      - 缺点：本轮被删除的节点会多存在一轮，在下一轮才会被清理
    - 混合写屏障(V1.8以后)
      - GC开始将栈上的对象全部扫描并且标记为黑色(无STW)
      - GC期间，任何在栈上创建的新对象，均为黑色
      - 被删除的对象标记为灰色
      - 被添加的对象标记为灰色
    - 混合写屏障场景
      - 对象被一个堆对象删除引用，成为栈对象的下游

### golang触发GC的时机
- 内存分配达到阀值
  - 上次GC内存分配量 * 内存增长率
  - 内存增长率由环境变量控制, 默认100，当前内存扩大一倍启动GC
- 定期触发GC， 默认2min
- 手动触发GC, runtime.GC() 

### 如何减少GC的触发
- 函数尽量不要返回map、slice对象
- 小对象合并
- 函数频繁创建的简单的对象，直接返回对象，效果比指针好
- 类型转换要注意
- 避免反复的创建slice