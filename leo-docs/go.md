# go面试遇到的问题

## go内存泄露怎么排查
用pprof定位，一般是Goroutine没有退出导致的。

1. goroutine开启的数量过多
2. 内存逃逸过多

- 从channel中读或写，但没有对应的写或读
- 在使用select时，所有的case都阻塞，没有正确退出，导致goroutine一直存在
- Goroutine进入死循环

- 在Goroutine中使用到了channel时，要考虑清楚channel何时可能会阻塞，以及阻塞时的具体情况
- 创建了一个Goroutine时，就要考虑清楚Goroutine应该如何结束
- 注意代码程序的逻辑，切忌在代码中出现死循环

## go为什么支持高并发
Go 语言的 Goroutine 和 Channel 
协程需要的资源少，是发生在用户态的，不
gpm调度

## go和php在使用过程中的区别
Go 相比 PHP 而言，有着更好的性能。数百个基准测试测试了在多种情况下的 Go 和 PHP，大部分情况下，Go 语言明显是赢家。最重要的是，在开发中会感觉 Go 语言更快 —— 测试运行速度更快，内存使用率更高效，CPU 使用率更低。

PHP 社区多于 Go 语言，其支持程度令人惊讶。我知道在使用 Go 的时候有一些不好的事情，例如代码生成器，这是 PHP 社区几年前留下来的东西。

静态类型支持



## 怎么保证主协程等待所有的协程执行完毕
1. wg  add done wait
2. 缓冲的channel for循环十个读取
3. 无缓冲的channel 

## mq怎么保证顺序执行
kafuka ，写topic的partition时候可以指定key，这样可以保写入的时候都是在一个partition

消费的时候，有两种方式
1. 一个 topic，一个 partition，一个 consumer，内部单线程消费，单线程吞吐量太低
2. 一个topic，一个partition，  一个消费者写多个队列，然后，一个队列由一个协程去消费，这样就能保证顺序性
   

## 怎么超时或者取消协程执行
context ，启动协程的时候传入
timer定时器，超时的时候可以取消


## struct 能不能对比
在 Go 语言中，Go 结构体有时候并不能直接比较，当其基本类型包含：slice、map、function 时，是不能比较的。若强行比较，就会导致出现例子中的直接报错的情况。

这样就可以保证两者的比较。如果我们被迫无奈，被要求一定要用结构体比较怎么办？
这时候可以使用反射方法 reflect.DeepEqual
## join 怎么设计数据结构
嵌套的方式

## mysql的隔离级别
未提交读，提交读，可重复度，串行化

## binglog redolog undolog

## mysql 怎么保证幻读问题
https://blog.csdn.net/m0_71777195/article/details/126084818

1. 快照读历史数据－mvcc
2. 当前读最新数据－next-key lock

## 服务治理做了哪些
idl的迁移和推动各方进行修改。

## 单线程的redis为什么快


## 内存逃逸会造成什么，怎么形成的
## sync.map 是怎么实现的
## 高并发怎么无锁保证顺序 wg sync
## go怎么保证顺序
## 隐式强制转换
## 数据类型封包解包
## interface 怎么判断类型

## mysql 为什么到了2000万数据性能急剧下降
## zset 用的底层数据结构是什么
## 微服务演进数据依赖
## kafuka 怎么重试
## 数据一致性

