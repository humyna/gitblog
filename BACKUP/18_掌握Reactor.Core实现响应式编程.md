# [掌握Reactor Core实现响应式编程](https://github.com/humyna/gitblog/issues/18)

# Reactor Core介绍
Reactor Core是一个基于Java 8的库，它实现了响应式编程模型，使得开发者能够构建高效、可伸缩、响应式的应用程序。它是建立在Reactive Streams规范之上的，这个规范定义了一套标准，用于构建异步的、基于事件的数据流处理系统。

Reactive Streams规范的核心是提供一种异步流程处理的方法，它允许不同组件之间以非阻塞的方式交换数据流。这种规范特别适用于处理大量的事件，这些事件需要被系统以异步的方式快速响应和处理。例如，在金融应用中，可能会有成千上万的股票价格更新每秒发生，Reactor Core能够帮助这样的应用快速地处理这些高频率的数据流。

Reactor Core提供了两个基本的响应式类型：Flux和Mono。Flux代表一个包含0到N个元素的异步序列，而Mono代表一个包含0到1个元素的异步序列。这两种类型都支持各种操作符，可以用来创建、转换、过滤和组合数据流。

通过使用Reactor Core，开发者可以利用响应式编程的优势，例如更好的资源利用率、更简洁的并发代码以及更好的错误处理机制。这使得Reactor Core成为构建现代、高性能、响应式Java应用程序的强大工具。

# Maven 依赖
```
<dependency>
    <groupId>io.projectreactor</groupId>
    <artifactId>reactor-core</artifactId>
    <version>3.4.16</version>
</dependency>

<dependency> 
    <groupId>ch.qos.logback</groupId> 
    <artifactId>logback-classic</artifactId> 
    <version>1.2.6</version> 
</dependency>
```

官网：https://projectreactor.io/docs

# 生成数据流
Flux和Mono都为响应式流publisher接口的实现。主要是为了生成数据流。这两个类都遵照规范，我们可以在下面代码中使用该接口:
`Publisher<String> just = Mono.just("foo");`

## Flux
Flux能够产生0~N个元素的数据流，示例代码如下：
`Flux<Integer> just = Flux.just(1, 2, 3, 4);`
上述代码产生了4个元素的静态数据流。

## Mono
使用Mono类，能够产生0~1个元素，示例代码：
`Mono<Integer> just = Mono.just(1);`
上述代码行为似乎与上节Flux一致，只是限制元素数量不超过1。

# 订阅流
## 收集元素
使用subsribe()方法收集流中的元素，代码如下：
` List<Integer> elements = new ArrayList<>();

        Flux.just(1, 2, 3, 4)
                .log()
                .subscribe(elements::add);`
只有当我们订阅了数据才会开始流动。另外还添加了日志记录，这有助于我们调试程序、查看后台发生了什么。

## 元素流
通过日志，可以看到完整数据流过程。

1. onSubscribe() – 当订阅流时调用
2. request(unbounded) – 当调用subscribe()方法, 意味着我们正创建订阅从流中请求元素。这种缺省情况为无边界的，即请求所有单个有效元素、
3. onNext() – 对每个单个元素都调用
4. onComplete() – 接收到最后一个元素后调用。实际上还有一个onError()，如果有异常就会调用它。

这是在Subscriber接口定义的，作为响应式流规范的一部分实现。实际上在调用onSubscribe()时在幕后实例化Subscriber。
`List<Integer> elements = new ArrayList<>();

    Flux.just(1, 2, 3, 4)
            .log()
            .subscribe(new Subscriber<Integer>() {
                @Override
                public void onSubscribe(Subscription s) {
                    s.request(Long.MAX_VALUE);
                }

                @Override
                public void onNext(Integer integer) {
                    elements.add(integer);
                }

                @Override
                public void onError(Throwable t) {}

                @Override
                public void onComplete() {}
            });`
我们能看到Subscriber 实现种每个方面对应可能的处理阶段。Flux提供的助手方法subscribe()避免了大量冗余工作。

## 与Java8流对比
核心区别在于，反应式是一个推模型，而Java 8流是一个拉模型。

# 其他操作
## 反压
响应式编程一个主要目标就是要解决反压问题。如果生产者发射事件速度超过消费者处理速度，最终消费者会因为过量事件而耗尽资源。

## 流操作
映射数据可以使用map()、合并流可以使用zip()等

## 热流
现实中响应式场景更可能面对是无限流。举例，持续不断鼠标运动事件流需要响应。这种类型流称为热流，因为它们总是在运行，可以在任何时间点订阅，忽略开始数据。

创建热流的一种方式为转换冷流。
节流可以引入sample方法，并指定参数：间隔时间。还有很多策略可以减少数据推送给下游，比如窗口和缓存


## 并发
Scheduler接口提供了异步执行的抽象，为我们提供一些实现。可以尝试订阅在非主线程中执行。
`List<Integer> elements  = new CopyOnWriteArrayList<>();
        Flux.just(1, 2, 3, 4)
                .log()
                .map(i -> i * 2)
                .subscribeOn(Schedulers.parallel())
                .subscribe(elements::add);`
并行调度让订阅运行在不同的线程


参考：
https://blog.csdn.net/neweastsun/article/details/127091096

