# 10. TODO

* 并发与IO
  * Lua 协程 coroutine
  * Go goroutine
  * Ruby 纤程 进程
  * Javascript 单线程

* 枚举类型: Go

* 输出/格式化输出

* 字符编码比较

* 环境与命令行参数

* Go slice

* 字符串模板

* Go: 迭代器

* 尾递归优化

* GC

* 文档

* 单元测试

---

## Go

> Do not communicate by sharing memory;instead, share memory by communicating.

### Channel 原理

#### 特点

* goroutine-safe，多个 goroutine 可以同时访问一个 channel 而不会出现竞争问题
* 可以用于在 goroutine 之间存储和传递值
* 其语义是先入先出（FIFO）
* 可以导致 goroutine 的 block 和 unblock

#### 实现: 带锁的队列

```go
type hchan struct {
  qcount   uint           //当前元素个数
  dataqsiz uint           //channel大小
  buf      unsafe.Pointer //元素指针数组, 一个环形队列
  elemsize uint16         //元素大小
  closed   uint32
  elemtype *_type         //元素类型
  sendx    uint           //send index
  recvx    uint           //receive index
  recvq    waitq          //等待接收的goroutine
  sendq    waitq          //等待发送的goroutine
  lock mutex              //锁
}
```

waitq 是一个链表结构的队列，每个元素是一个 sudog 的结构:

```go
type sudog struct {
  g          *g //  正在等候的 goroutine
  elem       unsafe.Pointer // 指向需要接收、发送的元素
  ...
}
```

`ch := make(chan Task, 3)`

ch 指向hchan结构体, 本身是一个指针

`ch <- task0` `ch <- task0` 都是**内存复制**

#### 阻塞过程

`ch <- task4` 执行的时候, channel 中已经满了, 需要pause 当前goroutine G1:

* G1给自己创建一个sudog, 追加到sendq队列
* G1 会调用运行时的 `gopark`
* Go 的运行时调度器就会接管
* G1状态设置为`waiting`
* G1和M断开(switch out), M空闲出来安排其他任务
* 从P队列选取可运行的新G, 新G和M建立关系(Switch in)

 最终, G1被阻塞, 新G开始运行, 但对操作系统线程M来说, 根本没有阻塞.

#### 恢复过程

G2 调用`t := <- ch`, 会导致G1 unblock:

* G2 先执行 `dequeue()` 从缓冲队列中取得 task1 给 t
* G2 从 sendq 中弹出一个等候发送的 sudog
* sudog中elem的值 `enqueue()` 到 buf
* sudog 中的 goroutine，也就是 G1，状态从 waiting 改为 runnable
* 调度器将 G1 压入 P 的运行队列，因此在将来的某个时刻调度的时候，G1 就会开始恢复运行

### 优雅关闭

#### channel 关闭相关的性质:

* 已经关闭的channel, 如果还有数据, 读取时会正常返回数据, 标志位也是false. 因此在不能更改channel状态的情况下，没有简单普遍的方式来检查channel是否已经关闭了

  `b, ok := <-c` ok 如果为false, 表示channel关闭且没有数据了

* 关闭已经关闭的channel会导致panic，所以在closer(关闭者)不知道channel是否已经关闭的情况下去关闭channel是很危险的

* 发送值到已经关闭的channel会导致panic，所以如果sender(发送者)在不知道channel是否已经关闭的情况下去向channel发送值是很危险的

* Golang禁止关闭只接收（receive-only）的channel

#### The Channel Closing Principle:

* 不要从接收端关闭channel

  使用单向channel(receive-only) 可以避免

* 要关闭有多个并发发送者的channel, 应该由最后一个存活的sender来关闭.

#### 如何关闭:

##### 打破channel closing principle的解决方案

从接收端（receiver side）关闭channel或者在多个发送者中的一个关闭channel

* send 和 close 都使用 panic/recover

* 或者把channel和锁封装起来, 包装的close方法用锁来保护起来, 只允许一次close.

##### 保持channel closing principle的优雅方案

* M个receivers，一个sender

  sender通过关闭data channel说“不再发送”

* 一个receiver，N个sender 

  receiver通过关闭一个额外的signal channel说“请停止发送”

* M个receiver，N个sender

  TODO
