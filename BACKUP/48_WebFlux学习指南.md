# [WebFlux学习指南](https://github.com/humyna/gitblog/issues/48)


## 1. 基础知识准备

### 1.1 前置知识
- Java 8+ Lambda表达式和Stream API
- Spring Framework基础
- 响应式编程概念
- 函数式编程思维

### 1.2 学习资源
```xml
<!-- 基础项目依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
<dependency>
    <groupId>io.projectreactor</groupId>
    <artifactId>reactor-test</artifactId>
    <scope>test</scope>
</dependency>
```

## 2. 循序渐进的学习路径

### 2.1 第一阶段：响应式编程基础
```java
// 1. 理解Mono和Flux
public class BasicExample {
    public void monoExample() {
        // 创建和操作单个元素
        Mono<String> mono = Mono.just("Hello")
            .map(str -> str + " World")
            .doOnNext(System.out::println);
    }

    public void fluxExample() {
        // 创建和操作多个元素
        Flux<Integer> flux = Flux.range(1, 5)
            .map(i -> i * 2)
            .filter(i -> i > 5);
    }
}
```

### 2.2 第二阶段：基本操作符使用
```java
public class OperatorExample {
    // 转换操作
    public Flux<String> transformExample() {
        return Flux.just("A", "B", "C")
            .map(String::toLowerCase)
            .flatMap(this::processString);
    }

    // 过滤操作
    public Flux<Integer> filterExample() {
        return Flux.range(1, 10)
            .filter(i -> i % 2 == 0)
            .take(3);
    }

    // 组合操作
    public Flux<String> combineExample() {
        Flux<String> flux1 = Flux.just("A", "B");
        Flux<String> flux2 = Flux.just("C", "D");
        return Flux.concat(flux1, flux2);
    }
}
```

### 2.3 第三阶段：错误处理
```java
public class ErrorHandlingExample {
    public Mono<String> handleErrors() {
        return Mono.just("data")
            .map(this::processData)
            .onErrorReturn("默认值")
            .onErrorResume(error -> Mono.just("恢复值"))
            .doOnError(error -> log.error("发生错误", error))
            .retry(3);
    }

    public Flux<String> handleErrorsWithBackup() {
        return mainFlux()
            .onErrorResume(error -> backupFlux())
            .timeout(Duration.ofSeconds(5));
    }
}
```

### 2.4 第四阶段：WebFlux控制器
```java
@RestController
@RequestMapping("/api")
public class ReactiveController {
    @GetMapping("/simple")
    public Mono<String> simpleEndpoint() {
        return Mono.just("Hello WebFlux");
    }

    @GetMapping(value = "/stream", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public Flux<ServerSentEvent<String>> streamEndpoint() {
        return Flux.interval(Duration.ofSeconds(1))
            .map(sequence -> ServerSentEvent.<String>builder()
                .data("SSE Event " + sequence)
                .build());
    }

    @PostMapping("/users")
    public Mono<User> createUser(@RequestBody Mono<User> userMono) {
        return userMono
            .flatMap(userService::saveUser)
            .doOnSuccess(user -> log.info("User created: {}", user));
    }
}
```

### 2.5 第五阶段：WebClient使用
```java
@Service
public class WebClientExample {
    private final WebClient webClient;

    public WebClientExample(WebClient.Builder builder) {
        this.webClient = builder.baseUrl("https://api.example.com").build();
    }

    public Mono<User> getUser(String id) {
        return webClient.get()
            .uri("/users/{id}", id)
            .retrieve()
            .bodyToMono(User.class);
    }

    public Flux<User> getAllUsers() {
        return webClient.get()
            .uri("/users")
            .retrieve()
            .bodyToFlux(User.class);
    }
}
```

## 3. 实战项目练习

### 3.1 构建响应式REST API
```java
@RestController
@RequestMapping("/api/products")
public class ProductController {
    private final ProductService productService;

    @GetMapping
    public Flux<Product> getAllProducts() {
        return productService.findAll();
    }

    @GetMapping("/{id}")
    public Mono<ResponseEntity<Product>> getProduct(@PathVariable String id) {
        return productService.findById(id)
            .map(ResponseEntity::ok)
            .defaultIfEmpty(ResponseEntity.notFound().build());
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public Mono<Product> createProduct(@RequestBody Product product) {
        return productService.save(product);
    }
}
```

### 3.2 实现实时数据流
```java
@Service
public class StockPriceService {
    public Flux<StockPrice> getStockPriceUpdates() {
        return Flux.interval(Duration.ofSeconds(1))
            .map(i -> new StockPrice("STOCK-" + i, 
                Random.nextDouble() * 100))
            .take(Duration.ofMinutes(5));
    }
}

@Controller
public class StockController {
    @GetMapping(value = "/stocks/price-updates", 
                produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public Flux<StockPrice> streamStockPrices() {
        return stockPriceService.getStockPriceUpdates()
            .doOnNext(price -> log.info("Sending price: {}", price));
    }
}
```

## 4. 测试技巧

### 4.1 单元测试
```java
@SpringBootTest
class ReactiveServiceTest {
    @Autowired
    private ReactiveService service;

    @Test
    void testMonoOperation() {
        StepVerifier.create(service.processData())
            .expectNext("processed data")
            .verifyComplete();
    }

    @Test
    void testFluxOperation() {
        StepVerifier.create(service.generateNumbers())
            .expectNext(1, 2, 3)
            .expectComplete()
            .verify();
    }
}
```

### 4.2 集成测试
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class ReactiveControllerTest {
    @Autowired
    private WebTestClient webTestClient;

    @Test
    void testEndpoint() {
        webTestClient.get().uri("/api/data")
            .exchange()
            .expectStatus().isOk()
            .expectBodyList(String.class)
            .hasSize(3);
    }
}
```

## 5. 性能优化和最佳实践

### 5.1 性能优化
```java
@Configuration
public class WebFluxConfig {
    @Bean
    public ReactorResourceFactory reactorResourceFactory() {
        ReactorResourceFactory factory = new ReactorResourceFactory();
        factory.setUseGlobalResources(false);
        factory.setLoopResources(LoopResources.create("event-loop", 1, 4, true));
        return factory;
    }

    @Bean
    public HttpHandler httpHandler(WebHandler webHandler) {
        return WebHttpHandlerBuilder.webHandler(webHandler)
            .filters(filters -> filters.add(new MetricsWebFilter()))
            .build();
    }
}
```

### 5.2 最佳实践示例
```java
@Service
public class OptimizedService {
    // 使用缓存
    private final Map<String, Mono<Data>> cache = new ConcurrentHashMap<>();

    public Mono<Data> getData(String key) {
        return cache.computeIfAbsent(key, k -> 
            fetchData(k).cache(Duration.ofMinutes(5)));
    }

    // 并行处理
    public Flux<Result> processItems(Flux<Item> items) {
        return items
            .parallel()
            .runOn(Schedulers.boundedElastic())
            .flatMap(this::processItem)
            .sequential();
    }

    // 背压处理
    public Flux<Data> streamData() {
        return Flux.create(sink -> {
            // 实现背压感知的数据流
            dataSource.subscribe(
                data -> {
                    if (!sink.isCancelled()) {
                        sink.next(data);
                    }
                },
                sink::error,
                sink::complete
            );
        }, FluxSink.OverflowStrategy.BUFFER);
    }
}
```

## 6. 进阶主题

### 6.1 响应式安全
```java
@Configuration
@EnableWebFluxSecurity
public class SecurityConfig {
    @Bean
    public SecurityWebFilterChain springSecurityFilterChain(
            ServerHttpSecurity http) {
        return http
            .authorizeExchange()
            .pathMatchers("/public/**").permitAll()
            .anyExchange().authenticated()
            .and()
            .oauth2Login()
            .and()
            .build();
    }
}
```

### 6.2 响应式数据访问
```java
@Repository
public interface ReactiveUserRepository 
    extends ReactiveCrudRepository<User, String> {
    
    Flux<User> findByAgeGreaterThan(int age);
    
    Mono<User> findByEmail(String email);
}
```

## 学习建议

1. **循序渐进**
   - 先掌握基础概念
   - 从简单示例开始
   - 逐步增加复杂度

2. **实践为主**
   - 动手编写代码
   - 实现小型项目
   - 解决实际问题

3. **调试技巧**
   - 使用 `log()` 操作符
   - 熟练使用调试工具
   - 理解响应式堆栈跟踪

4. **常见陷阱避免**
   - 避免阻塞操作
   - 正确处理订阅
   - 合理使用操作符

5. **持续学习**
   - 关注版本更新
   - 参与社区讨论
   - 阅读源码理解原理

通过这个系统的学习路径，您可以逐步掌握WebFlux的各个方面。记住，响应式编程需要一个思维方式的转变，需要时间和耐心来适应这种新的编程范式。

----
文章由AI生成