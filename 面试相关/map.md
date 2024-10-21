# Go Map
## Map 简介
在Go语言中提供了map数据结构来存储键值对数据。map的数据类型为`map[K]V`，其中K为键的类型，V为值的类型。map的键类型必须支持`==`操作符，用来比较两个键是否相等。Go语言提供了4种内置的map操作: `len`、`delete`、`comparison`、`assign`。

## Map 定义
```go
map_var := make(map[K]V) // 用make函数创建一个空的map，其中K和V分别为键和值的类型
map_var[key] = value     // 向map中添加一个键值对
value := map_var[key]    // 获取指定键的值
delete(map_var, key)     // 从map中删除指定的键及其对应的值
```

## Map Iteration
Go语言提供了两个方法来遍历map中的所有键值对，分别是`range`方法和`Len()`方法。
```go
// 使用range循环遍历map中的所有键值对
for key, value := range map_var {
    // TODO ...
}

// 计算map中的元素数量
if len(map_var) > 0 {
    // TODO ...
}
```

## Map 的线程安全
在Go语言中，map是**非线程安全的**，在多线程并发访问时可能导致程序报错。当map被多个协程同时访问时，我们需要使用sync包中的`sync.Mutex`来确保操作的原子性和并发安全。

```go
import "sync"

type SafeMap struct {
    mu sync.Mutex
    m  map[string]int
}

func (sm *SafeMap) Get(key string) int {
    sm.mu.Lock()
    defer sm.mu.Unlock()
    return sm.m[key]
}

func (sm *SafeMap) Set(key string, value int) {
    sm.mu.Lock()
    defer sm.mu.Unlock()
    sm.m[key] = value
}

func (sm *SafeMap) Delete(key string) {
    sm.mu.Lock()
    defer sm.mu.Unlock()
    delete(sm.m, key)
}
```
# map 底层原理
Go语言的map在设计上是一种**哈希表**的数据结构。它利用哈希函数将键映射到不同的存储空间，从而实现高效的查找和插入操作。

## 哈希函数
哈希函数将字符串映射到一个整数上，这称为哈希值。不同的字符串可能会有相同的哈希值，但相同的字符串必定具有相同的哈希值。哈希函数需要满足两点：
* 哈希函数的计算结果必须是非负整数，因为负数无法在数组中表示。
* 两个不同字符串的哈希值尽量不要相等，这样可以避免在查找时产生冲突。

在Go语言中，字符串的哈希函数采用的是FNV-1哈希算法，算法代码如下：
```go
const (
    offset64 = 14695981039346656037
    prime64  = 1099511628211
)

func stringHash(s string) uint64 {
    h := uint64(offset64)
    for i := 0; i < len(s); i++ {
        h ^= uint64(s[i])
        h *= prime64
    }
    return h
}
```

## 哈希冲突
在哈希表中，哈希值相同的多个字符串可能会存储在同一个位置上，这种现象叫做**哈希冲突**。哈希冲突处理策略有开放寻址法、再哈希法和链地址法。
- 开放寻址法：将发生冲突的条目逐个检索新的空棑直到找到一个空位置来存储当前键值对
- 再哈希法：对于发生冲突的键，用另一个不同的哈希函数计算地址
- 链地址法：对于发生冲突的键，将其存储在一个链表中

Go语言使用**链地址法**处理哈希冲突。对于每个存储单元，map结构体中还维护了一个`[]keyValue`类型的链表。
```go
type hmap struct {
    count     int // 映射中的键值对数量
    flags     uint8 // 控制哈希表的一些属性
    B         uint8 // 用于计算哈希地址的初始大小
    noverflow uint16 // 链表上的溢出桶的数量
}
```

## Growing
在Go语言中，动态数组会自动地为map分配更多的空间。Growing过程涉及到将原始的数组重新复制到一个更大的数组中，其中原数组的元素需要重新计算其在新数组中的位置，而新数组的元素则需要将其键值对填充到相应的位置。Growing的过程比较复杂，可以由函数`hashGrow()`来控制。

```go
// hashGrow() 将map的数组的大小翻倍，并处理哈希冲突。
func hashGrow(h *hmap) {
    // ...
    buf := make([]keyValue, newCap)
    //...
    for i := uintptr(0); i < cap; i++ {
        // ...
        evacuate(h, &h.oldbuckets[i], &buf)
        // ...
    }
    // ...
}

// evacuate() 将一个bucket中的键值对重新映射到新的数组中
func evacuate(h *hmap, oldbuck *bucket, newbuck *[]keyValue) {
    // ...
}
```

## map扩容
### 双倍扩容
Go语言中的哈希表在map的数组容量达到一定程度时，就会自动进行扩容。扩容的依据是当前已存储的元素数量和数组的长度之间的比值：
* 当map的已存储元素数量小于map数组长度的一半时，元素的数量未达到哈希表效率的最大值，无需扩容；
* 当map已存储的元素数量大于等于map数组长度的一半时，哈希表的查找效率已达到最大值，所以需要扩容。

Go语言的map会优先选择数组大小为原数组大小的2倍，以确保map在存储过程中有足够的空间存放新的元素。当元素数量达到85%时，Go语言就会再次对数组进行扩容，此时数组长度翻倍，以保证数组长度和元素数量的比例始终维持在0.75左右，以平衡效率和空间占用。

### Growing过程
当映射中的元素数量超过85%时，Go语言就会触发map的扩容过程。在扩容的过程中，map会将原有的元素复制到新的数组中，并将新数组的初始大小设置为原数组的2倍。对于发生哈希冲突的元素，需要在新的数组中重新计算哈希地址。


### 避免溢出
当数组中元素的数量超过`0x7fffffff`(2^31-1，即int类型的最大值)时，就会发生溢出，此时数组的大小将无法达到原数组的2倍。所以Go语言会在初始创建map时，为其初始化一个较小的数组，并设置map的B值，以便在元素数量超过限制时再次进行扩容。当map中元素的数量超过阈值时，会再次翻倍，直到数组大小小于`0x7fffffff`为止。

## 代码分析
`hashmap.go`包含在Go语言源码中的`src/container/map.go`文件中。其中`map`结构体的定义和Growing实现都在`runtime`包中，在`src/runtime/map.go`文件中。


## 附录
* 为什么哈希表的容量要设置为2的n次幂？为什么不是其他数字？
* Go语言中的map是如何进行线程安全的？原理是什么？
* map的数据结构是怎样的？如何实现键值对的查找、添加、删除操作？
* 如何实现Growing过程？
* 为什么map的扩容条件是85%，而不是100%？
* 在go语言中如何创建map？
* 为什么哈希冲突处理策略有开放寻址法、再哈希法和链地址法？
* 如果存在冲突，键值对是如何存储在数组中的？
* 为什么Growing过程中会创建一个较大的临时数组，而不是直接在原数组上扩展空间？
* 如何实现map的迭代？

## 总结
本节我们学习了Go语言中的map数据类型，使用方法以及map的数据安全问题。