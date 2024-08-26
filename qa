1. golang抽象出用户态的线程（groutine）的优点是什么
Go 语言的 goroutine 是一种用户态线程，相比于传统的操作系统级别的线程，它具有以下优点：
- 更轻量：每个 goroutine 的堆栈内存占用量只有几 kb，远远小于操作系统线程。这使得在 Go 语言中可以同时运行数以百万计的 goroutine。
- 低延迟的调度：goroutine 的调度由 Go 运行时进行，不依赖于操作系统的线程调度，因此可以实现更快速的任务切换。
- 简单的同步机制：Go 语言提供了 channel 和 select 关键字，使得在 goroutine 之间进行同步和通信变得非常简单。
- 更高效的利用多核：Go 语言的运行时可以智能地将 goroutine 分配到多个系统线程上运行，从而更有效地利用多核 CPU。
- 更简洁的代码：由于 goroutine 的使用方式非常简单，只需要在函数调用前加上 go 关键字即可，所以在编写异步或并发代码时，goroutine 可以让代码保持简洁和清晰。
- 优雅的错误处理：Go 语言的 panic 和 recover 机制可以方便地处理和恢复 goroutine 的运行时错误。

2. golang的垃圾回收机制


MySQL
1. 介绍一下mvcc
2. MySQL是如何保证数据不丢的
3. binlog和undolog的区别


