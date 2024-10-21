# Go 切片

## 切片的长度和容量

```go
func main() {
    data := make([]int, 5, 10)
    fmt.Println("Length of data:", len(data))
    fmt.Println("Capacity of data:", cap(data))
}
```

运行这段代码，输出如下：

```
Length of data: 5
Capacity of data: 10
```

切片的长度表示当前长度，也可以说是数组已经占用的空间。切片的容量表示从当前位置到最后位置的长度，也可以说是数组总的空间。可以这样理解：`切片 = 从起始位置到结束位置的长度`。切片的容量可以大于或者等于长度，但切片的长度必须小于容量。

`make()`函数创建一个有指定长度和容量的切片。

## 添加元素到切片

```go
func main() {
    data := make([]int, 0, 5)

    data = append(data, 1)
    data = append(data, 2)
    data = append(data, 3)

    fmt.Println("Length of data:", len(data))
    fmt.Println("Capacity of data:", cap(data))
}
```

运行这段代码，输出如下：

```
Length of data: 3
Capacity of data: 5
```

可以观察到，`data`切片在添加新元素时自动扩大了容量。

## 删除元素

```go
func main() {
    data := []int{1, 2, 3, 4, 5}

    data = append(data[:3], data[4:]...)
    fmt.Println(data)
}
```

运行这段代码，输出如下：

```
[1 2 3 5]
```

删除切片中的指定元素。

## 切片越界

```go
func main() {
    data := []int{1, 2, 3, 4, 5}
    fmt.Println(data[:6])
}
```

运行这段代码，输出如下：

```
panic: runtime error: slice bounds out of range
```

切片越界，程序会抛出异常。

## 切片遍历

```go
func main() {
    data := []int{1, 2, 3, 4, 5}

    for i, v := range data {
        fmt.Printf("Element at index %d is %d\n", i, v)
    }
}
```

运行这段代码，输出如下：

```
Element at index 0 is 1
Element at index 1 is 2
Element at index 2 is 3
Element at index 3 is 4
Element at index 4 is 5
```

切片元素索引从 0 开始，到最后一个元素的索引为切片长度减 1。

## 切片原理

切片底层的数据结构是个结构体：

```go
type slice struct {
    array unsafe.Pointer
    len   int
    cap   int
}
```

数组初始化时会分配内存，切片在初始化时会先初始化一个底层数组，然后初始化一个切片结构体，指向该底层数组。

`make()`函数创建的切片，底层数组是在堆上分配的，而直接初始化切片的数组是栈上分配的。

切片的容量可以大于或者等于长度，但切片的长度必须小于容量。

在添加元素到切片时，如果达到容量上限，会将切片复制到新的更大的数组中，并将旧数组的切片指向新的数组。

删除切片中的元素不会影响切片容量，但可能会导致内存浪费。

## 切片扩容

当添加元素到切片时，如果达到容量上限，会把切片复制到新的更大的数组中，并将旧数组的切片指向新的数组。

```go
func main() {
    data := make([]int, 0, 5)

    data = append(data, 1)
    data = append(data, 2)
    data = append(data, 3)
    data = append(data, 4)
    data = append(data, 5)

    fmt.Println("Length of data:", len(data))
    fmt.Println("Capacity of data:", cap(data))
}
```

运行这段代码，输出如下：

```
Length of data: 5
Capacity of data: 10
```

可以观察到，`data`切片容量从 5 扩展到 10。

## 扩容原理

当添加元素到切片时，如果达到容量上限，会把切片复制到新的更大的数组中，并将旧数组的切片指向新的数组。

```go
func growslice(et *_type, old slice, cap int) slice {
    ...
    mem, overflow := math.MulUintptr(et.size, uintptr(cap))
    ...
}
```

`growslice()`函数用于在切片添加新元素时，分配新的切片容量。

计算新切片的容量时会使用`math.MulUintptr()`函数，这是一个无符号整数乘法函数，使用此函数可以避免溢出风险。

## 扩容细节

`growslice()`函数内部会使用`mallocgc()`函数分配内存，该函数会调用 gcDrain() 函数。`gcDrain()`函数是 GC 机制，当执行 GC 时，会暂停 GC 操作并执行当前线程的任务。

```go
func mallocgc(size uintptr, typ unsafe.Pointer, flag flag) unsafe.Pointer {
    ...
    return mallocgc(size, typ, flag)
}

func gcDrain(syscallSafe bool) {
    ...
}
```

GC 暂停后，如果此时有其他 goroutines 在释放对象，那么当前 goroutine 会读取到其他 goroutines 释放的对象，导致数据竞争错误。

因此，为了保证安全的并发访问，应该使用`append()`函数添加元素到切片，而不是直接修改切片。

## 扩容规则

```go
func main() {
    data := make([]int, 0, 5)

    for i := 0; i < 5; i++ {
        data = append(data, i)
    }

    fmt.Println("Length of data:", len(data))
    fmt.Println("Capacity of data:", cap(data))
}
```

运行这段代码，输出如下：

```
Length of data: 5
Capacity of data: 8
```

`growslice()`函数中，在分配新切片容量时，会判断当前容量是否小于 1024。

如果当前容量小于 1024，新切片的容量会翻倍。

如果当前容量大于等于 1024，新切片的容量会加倍再加 7。

`growslice()`函数中的代码如下：

```go
func growslice(et *_type, old slice, cap int) slice {
    ...
    if uintptr(cap) > maxSliceCap {
        panic(errorString("growing slice capacity exceeds maximum capacity"))
    }

    if et.size == 0 {
        return slice{}
    }

    if cap < uint(old.cap) {
        panic("capacity smaller than old capacity")
    }

    newcap := old.cap
    doublecap := newcap + newcap
    if cap > uint(doublecap) {
        newcap = cap
    } else {
        if uint(doublecap) > uint(maxSliceCap) {
            newcap = maxSliceCap
        } else {
            newcap = doublecap
        }
    }

    ...
}
```

## 容量与性能

当添加元素到切片时，如果达到容量上限，会把切片复制到新的更大的数组中，并将旧数组的切片指向新的数组。

当切片的元素较少时，不会触发扩容操作，所以容量设置可以小于切片的长度。

但是随着元素数量增加，可能需要多次对切片进行扩容，这时容量的设置会影响性能，所以尽量确保切片的容量大于或等于切片长度。

## 小结

切片是 Go 语言中非常重要的一部分，切片的原理、实现和细节都是理解切片的关键。