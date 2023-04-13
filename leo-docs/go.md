# go面试遇到的问题

## go内存泄露怎么排查
用pprof定位，一般是Goroutine没有退出导致的。

goroutine开启的数量过多

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

## binlog redolog undolog

redo log 持久性
undo log 原子性
mvcc 隔离性

### binlog
binlog用于记录数据库执行的写入性操作(不包括查询)信息，以二进制的形式保存在磁盘中。binlog是mysql的逻辑日志，并且由Server层进行记录，使用任何存储引擎的mysql数据库都会记录binlog日志。

在实际应用中，binlog的主要使用场景有两个，分别是主从复制和数据恢复。

### redo log
有一点懂了，但是不是很懂，知道这个是为了重做mysql挂的时候或者宕机的时候用的，但是为什么有了这个就能保证数据修改成功了呢？


为什么需要redo log,事务的四大特性里面有一个是持久性，具体来说就是只要事务提交成功，那么对数据库做的修改就被永久保存下来了，不可能因为任何原因再回到原来的状态。那么mysql是如何保证一致性的呢？最简单的做法是在每次事务提交的时候，将该事务涉及修改的数据页全部刷新到磁盘中。但是这么做会有严重的性能问题，主要体现在两个方面：
1）、 因为Innodb是以页为单位进行磁盘交互的，而一个事务很可能只修改一个数据页里面的几个字节，这个时候将完整的数据页刷到磁盘的话，太浪费资源了！
2）、 一个事务可能涉及修改多个数据页，并且这些数据页在物理上并不连续，使用随机IO写入性能太差！
因此mysql设计了redo log，具体来说就是只记录事务对数据页做了哪些修改，这样就能完美地解决性能问题了(相对而言文件更小并且是顺序IO)。
redo log包括两部分：一个是内存中的日志缓冲(redo log buffer)，另一个是磁盘上的日志文件(redo log file)。mysql每执行一条DML语句，先将记录写入redo log buffer，后续某个时间点再一次性将多个操作记录写到redo log file。这种先写日志，再写磁盘的技术就是MySQL里经常说到的WAL(Write-Ahead Logging) 技术。


### undo log

数据库事务四大特性中有一个是原子性，具体来说就是 原子性是指对数据库的一系列操作，要么全部成功，要么全部失败，不可能出现部分成功的情况。实际上，原子性底层就是通过undo log实现的。

undo log主要记录了数据的逻辑变化，比如一条INSERT语句，对应一条DELETE的undo log，对于每个UPDATE语句，对应一条相反的UPDATE的undo log，这样在发生错误时，就能回滚到事务之前的数据状态。同时，undo log也是MVCC(多版本并发控制)实现的关键。

## 数据一致性
整个过程中，数据修改都是在内存中，极大提升磁盘IO速度，而且将redo log提前写入磁盘。

如果整个事务执行的过程系统崩溃或者断电了，在系统重启的时候，恢复机制会将redo log中已提交的事务重做，保证事务的持久性；而undo log中未提交的事务进行回滚，保证事务的原子性。

## 服务治理做了哪些
idl的迁移和推动各方进行修改。

redis
## 单线程的redis为什么快

## redis 有那些淘汰策略
https://codeup.aliyun.com/634d054f67fa83af64bcfedd/fengxueguang/PHP-Interview-QA/blob/master/leo-docs%2F06.Redis%2FQA.md

## zset 用的底层数据结构是什么
正常的是跳表，还有一个压缩列表。

## 内存逃逸会造成什么，怎么形成的

## 读取一个关闭的channel会怎么样
读已关闭的channel

读已经关闭的channel无影响。

如果在关闭前，通道内部有元素，会正确读到元素的值；如果关闭前通道无元素，则会读取到通道内元素类型对应的零值。

若遍历通道，如果通道未关闭，读完元素后，会报死锁的错误。

fatal error: all goroutines are asleep - deadlock!

写已关闭的通道

会引发panic: send on closed channel

关闭已关闭的通道

会引发panic: close of closed channel 

从一个有缓冲的 channel 里读数据，当 channel 被关闭，依然能读出有效值。只有当返回的 ok 为 false 时，读出的数据才是无效的。

## sync.map 是怎么实现的
有一个read map(只读)， 一个dirty map,miss的次数多了就会把dirty提升为read

## 高并发怎么无锁保证顺序 wg sync

## go中原子性操作有哪些


## go怎么保证顺序
## go中chanel 和map是怎么实现的
map就是hash表

## go 数组和切片的区别
1.切片是指针类型，数组是值类型；
2.数组的长度是固定的，而切片不是（切片可以看成动态的数组）；
3.切片比数组多一个容量（cap）属性；
4.切片的底层是数组

## go 数组怎么转换为切片
- 循环赋值
- Copy（dst,src） 将src的值全部复制到dst中；copy函数是将于将内容从一个数组切片复制到另一个数组切片，如果带入数组参数是无法通过编译的所以我们这边要使用[:]来把数组「伪装」成一个切片。
- s:= arr[startIndex:endIndex]

## 隐式强制转换
## 数据类型封包解包

## interface 怎么判断类型
多个可以这样，
```
type Test struct {
    Test string
}

func test(value interface{}) {
    switch value.(type) {
    case string:
        // 将interface转为string字符串类型
        fmt.Println("value type is string")
    case int32:
        // 将interface转为int32类型
        fmt.Println("value type is int32")
    case int64:
        // 将interface转为int64类型
        fmt.Println("value type is int64")
    case Test:
        // 将interface转为Test struct类型
        fmt.Println("value type is Test struct")
    case []int:
        // 将interface转为切片类型
        fmt.Println("value type is Test []int")
    default:
        fmt.Println("unknown")
    }
}
```

```
func test(value interface{}) {
    if op, ok := value.(string); ok {
        fmt.Println("value type is string:", op)
    } else {
        fmt.Println("value type is not string")
    }
}
```
## mysql 为什么到了2000万数据性能急剧下降
一般这个B+树是三层，如果数据量达到一定程度，B+树的高度达到4层，这个时候性能就会下降。

## 微服务演进数据依赖
## kafuka 怎么重试
