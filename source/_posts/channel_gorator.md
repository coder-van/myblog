---
title: Go语言协程探索
date: 2017-05-06 20:30:21
tags:
- Go
- Goroutine
categories: code
---

``` code
func main() {
	// runtime.GOMAXPROCS(1) // 1 因素1 cpu
	ch := make(chan int)
  go func() {
		for{
			a := <- ch
      // for j:=0; j<1024*1024; j+=1{} // 2 因素2
      //	time.Sleep(time.Second)  // 3 因素3
			fmt.Println("goroutine 1 : ", a)
		}
	}()
	go func() {
		for{
			a := <- ch
      // for j:=0; j<1024*1024; j+=1{} // 2 因素2
      //	time.Sleep(time.Second)  // 3 因素3
			fmt.Println("goroutine 2 : ", a)
		}
	}()

	for i := 0; i < 20; i++ {
		ch <- i
	}
}  
```
上面被注释的三行代码表示三个因素
1. GOMAXPROCS 设置goroutine们由几个cpu分担执行 sets the maximum number of CPUs that can be executing
2. 使得协程变成cpu运算密集型
3. 使协程可以被挂起，出让cpu

注释掉 123 三行语句 得到结果1
```
goroutine 2 :  0
goroutine 2 :  2
goroutine 2 :  3
goroutine 2 :  4
goroutine 2 :  5
goroutine 1 :  1
goroutine 2 :  6
goroutine 2 :  8
goroutine 1 :  7
goroutine 2 :  9
goroutine 2 :  11
goroutine 2 :  12
goroutine 2 :  13
goroutine 1 :  10
goroutine 2 :  14
goroutine 1 :  16
goroutine 2 :  15
goroutine 1 :  17
goroutine 1 :  19
```
通过结果可以看到，由哪个协程执行不确定

当把因素1取消注释，一个cpu执行，得到结果2
```
goroutine 2 :  0
goroutine 2 :  1
goroutine 2 :  2
goroutine 2 :  3
goroutine 2 :  4
goroutine 2 :  5
goroutine 2 :  6
goroutine 2 :  7
goroutine 2 :  8
goroutine 2 :  9
goroutine 2 :  10
goroutine 2 :  11
goroutine 2 :  12
goroutine 2 :  13
goroutine 2 :  14
goroutine 2 :  15
goroutine 2 :  16
goroutine 2 :  17
goroutine 2 :  18

```
通过结果可以看到只有一个协程消费了整个channel
设置runtime.GOMAXPROCS(n) // n>=2 时，结果就如结果1

然后我们先注释掉因素1, 取消对因素2的注释
```
goroutine 2 :  0
goroutine 1 :  1
goroutine 2 :  2
goroutine 1 :  3
goroutine 2 :  4
goroutine 1 :  5
goroutine 2 :  6
goroutine 1 :  7
goroutine 2 :  8
goroutine 1 :  9
goroutine 2 :  10
goroutine 1 :  11
goroutine 2 :  12
goroutine 1 :  13
goroutine 2 :  14
goroutine 1 :  15
goroutine 2 :  16
goroutine 1 :  17
goroutine 2 :  18
```
可以看到让每个协程计算密集后，协程开始交替执行

注释掉因素1和, 取消注释因素3
```
goroutine 1 :  0
goroutine 2 :  1
goroutine 1 :  2
goroutine 2 :  3
goroutine 1 :  4
goroutine 2 :  5
goroutine 1 :  6
goroutine 2 :  7
goroutine 1 :  8
goroutine 2 :  9
goroutine 1 :  10
goroutine 2 :  11
goroutine 1 :  12
goroutine 2 :  13
goroutine 1 :  14
goroutine 2 :  15
goroutine 1 :  16
goroutine 2 :  17
goroutine 1 :  18

```
同样协程交替执行

在取消注释因素1和2，注释因素3情况下，得到结果是不确定的，通常是一个协程（不确定哪一个）消费了整个或大部分channel

试着增加协程cpu运算密集度，当计算增加到一定时候，协程又开始交替执行

最后取消注释因素1和3，注释2，得到结果同样是协程交替执行

总结：go在处理大的计算或者io的时候会均匀的分配cpu给goroutine,gorautine计算密度很小或没有发生IO等需要挂起和谦让执行机会的时候，goroutine不会出让cpu。。
