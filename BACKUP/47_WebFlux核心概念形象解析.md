# [WebFlux核心概念形象解析](https://github.com/humyna/gitblog/issues/47)

## 1. Flux（多个元素的流）

### 类比：传送带
想象一个工厂的传送带，可以持续传送多个物品。

```java
// 传统方式（类比：一次性搬运所有箱子）
List<Package> packages = warehouse.getAllPackages();

// Flux方式（类比：传送带持续运送）
Flux<Package> packageFlux = Flux.fromIterable(packages)
    .delayElements(Duration.ofSeconds(1))  // 每秒传送一个包裹
    .map(this::processPackage)            // 处理包裹
    .filter(pkg -> pkg.isQualified());    // 质量检查
```

### 实际应用示例
```java
@RestController
public class PackageController {
    @GetMapping(value = "/packages", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public Flux<Package> streamPackages() {
        return Flux.interval(Duration.ofSeconds(1))
            .map(i -> new Package("Package-" + i))
            .take(10);  // 限制只发送10个包裹
    }
}
```

## 2. Mono（单个元素的流）

### 类比：快递配送
想象等待一个快递包裹的配送过程。

```java
// 传统方式（类比：同步等待快递）
Package package = deliveryService.waitForPackage();

// Mono方式（类比：异步等待快递通知）
Mono<Package> packageMono = Mono.fromSupplier(() -> deliveryService.getPackage())
    .timeout(Duration.ofMinutes(5))    // 设置等待超时
    .doOnSuccess(pkg -> notifyReceived())  // 收到后通知
    .doOnError(err -> notifyFailed());     // 配送失败通知
```

### 实际应用示例
```java
@Service
public class DeliveryService {
    public Mono<Package> deliverPackage(String id) {
        return Mono.just(new Package(id))
            .delayElement(Duration.ofSeconds(2))  // 模拟配送时间
            .doOnNext(pkg -> log.info("Package {} delivered", pkg.getId()));
    }
}
```

## 3. Publisher（发布者）& Subscriber（订阅者）

### 类比：报纸订阅服务
想象一个报纸发行商和订阅者的关系。

```java
// 类比：报纸发行商
public class NewspaperPublisher {
    private Sinks.Many<Newspaper> newspaperSink = 
        Sinks.many().multicast().onBackpressureBuffer();

    // 发行新报纸
    public void publishNewspaper(Newspaper newspaper) {
        newspaperSink.tryEmitNext(newspaper);
    }

    // 获取订阅流
    public Flux<Newspaper> subscribe() {
        return newspaperSink.asFlux();
    }
}

// 使用示例
NewspaperPublisher publisher = new NewspaperPublisher();
publisher.subscribe()
    .subscribe(
        paper -> System.out.println("收到新报纸: " + paper),
        error -> System.out.println("配送出错: " + error),
        () -> System.out.println("订阅结束")
    );
```

## 4. Backpressure（背压）

### 类比：自助餐传菜带
想象一个自助餐厅的传菜带，顾客可以控制取菜速度。

```java
public class BuffetService {
    // 类比：厨房持续制作菜品
    public Flux<Dish> serveDishes() {
        return Flux.interval(Duration.ofSeconds(1))
            .map(i -> new Dish("Dish-" + i))
            .onBackpressureBuffer(10)    // 最多缓存10道菜
            .onBackpressureDrop(dish -> 
                log.warn("菜品{}因无人取用而丢弃", dish.getName()));
    }
}

// 顾客取菜（控制速度）
buffetService.serveDishes()
    .limitRate(2)    // 每次最多取2道菜
    .delayElements(Duration.ofSeconds(3))  // 每3秒取一次
    .subscribe(dish -> System.out.println("享用菜品: " + dish));
```

## 5. Operators（操作符）

### 类比：流水线加工
想象工厂流水线上的不同加工站。

```java
public class ProductionLine {
    public Flux<Product> processProducts() {
        return Flux.just(new Product("raw"))
            // map: 类比单个产品的加工
            .map(product -> product.process())
            
            // filter: 类比质量检查站
            .filter(product -> product.qualityCheck())
            
            // flatMap: 类比产品分解再组装
            .flatMap(product -> disassembleAndReassemble(product))
            
            // reduce: 类比产品装箱
            .reduce(new Box(), Box::addProduct);
    }
}
```

## 6. Error Handling（错误处理）

### 类比：产品质检流程
想象产品质检过程中的不同处理方案。

```java
public class QualityControl {
    public Mono<Product> inspectProduct(Product product) {
        return Mono.just(product)
            // onErrorReturn: 类比产品不合格时替换
            .map(this::inspect)
            .onErrorReturn(DefectException.class, getDefaultProduct())
            
            // onErrorResume: 类比返修流程
            .onErrorResume(RepairableException.class, 
                e -> repairProduct(product))
            
            // retry: 类比重复检查
            .retry(3)
            
            // timeout: 类比检查超时
            .timeout(Duration.ofSeconds(5));
    }
}
```

## 7. Scheduler（调度器）

### 类比：工作调度系统
想象工厂不同车间的工作分配。

```java
public class WorkScheduler {
    public Flux<Task> processTasks() {
        return Flux.just(new Task())
            // publishOn: 类比将任务转移到其他车间
            .publishOn(Schedulers.boundedElastic())
            .map(this::processTask)
            
            // subscribeOn: 类比指定起始车间
            .subscribeOn(Schedulers.parallel())
            
            // 类比不同类型任务的分配
            .parallel()
            .runOn(Schedulers.parallel())
            .sequential();
    }
}
```

## 8. Context（上下文）

### 类比：工作环境配置
想象不同工序需要的特定工作环境。

```java
public class WorkContext {
    public Mono<Product> assembleProduct() {
        return Mono.just(new Product())
            // 类比设置工作环境
            .contextWrite(context -> 
                context.put("assemblyLine", "A1")
                       .put("temperature", "25C"))
            
            // 类比使用特定环境
            .flatMap(product -> 
                Mono.deferContextual(ctx -> 
                    assemble(product, ctx.get("assemblyLine"))))
            
            // 类比清理环境
            .contextWrite(Context::empty);
    }
}
```

## 实际应用整合示例

```java
@Service
public class ProductService {
    public Flux<Product> processOrders(Flux<Order> orders) {
        return orders
            // 订单流水线处理
            .flatMap(order -> 
                Mono.just(order)
                    .map(this::validateOrder)        // 订单验证
                    .flatMap(this::reserveInventory) // 库存预留
                    .flatMap(this::createProduct)    // 产品制作
                    .timeout(Duration.ofMinutes(5))  // 处理超时
                    .onErrorResume(e -> handleError(order, e)) // 错误处理
                    .contextWrite(context -> 
                        context.put("orderId", order.getId())) // 上下文传递
            )
            // 背压控制
            .onBackpressureBuffer(100)
            // 并行处理
            .parallel()
            .runOn(Schedulers.boundedElastic())
            .sequential();
    }
}

@RestController
@RequestMapping("/api/products")
public class ProductController {
    @Autowired
    private ProductService productService;

    @GetMapping(produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public Flux<Product> streamProducts() {
        return productService.processOrders(orderStream)
            .doOnNext(product -> log.info("Product created: {}", product))
            .doOnError(error -> log.error("Error: {}", error))
            .doOnComplete(() -> log.info("Processing completed"));
    }
}
```

这些类比帮助我们更直观地理解WebFlux的核心概念。实际应用中，这些概念会相互配合，形成完整的响应式处理流程。理解这些概念对于正确使用WebFlux和构建高效的响应式应用至关重要。

--
全文由AI生成