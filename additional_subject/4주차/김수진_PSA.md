# PSA

> **Portable + Service Abstraction**
> 
1. **Service Abstraction (**서비스 추상화)
    
    → 실제로 서비스에서 사용되는 부분의 정보만을 제공하고, 불필요한 정보까지는 제공하지 않는다는 원칙
    

특정 기술을 쓸 때, 그 기술의 구체적인 구현 내용까지 알지 않아도 편리하게 쓸 수 있다.

1. Portable

→ 기술 스택이 바뀌어도 코드 변경 없이도 **다른 기술로 쉽게 전환 가능**

**Portable Service Abstraction**

> 특정 기술이나 환경에 종속되지 않는 방식으로 기술을 추상화하는 개념
> 

## Spring Web MVC**에서 PSA 적용**

### **기존 Servlet 방식 (Spring 없이 구현)**

```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException {
        response.getWriter().write("Hello, Servlet!");
    }
}
```

`HttpServlet`을 상속받고 `doGet()`을 직접 오버라이딩해야 한다.

`HttpServletRequest`, `HttpServletResponse`를 직접 다뤄야 해서 코드가 길어진다.

### **Spring Web MVC 방식 (PSA 적용)**

```java
@RestController
public class HelloController {

    @GetMapping("/hello")  // PSA 적용: Servlet API 없이도 요청을 처리 가능
    public String hello() {
        return "Hello, Spring MVC!";
    }
}
```

`@RestController`와 `@GetMapping`을 사용하면 **Servlet API를 직접 사용하지 않아도 된다.**

Spring이 내부적으로 `DispatcherServlet`을 사용하여 요청을 자동으로 처리한다.

### **Spring Web MVC → Spring WebFlux로 변경 가능**

### **Spring WebFlux 방식 (PSA 적용)**

```java
@RestController
public class HelloController {

    @GetMapping("/hello")
    public Mono<String> hello() { // PSA 적용: WebFlux에서도 같은 코드 사용 가능
        return Mono.just("Hello, Spring WebFlux!");
    }
}
```

 기존 `String` 반환을 `Mono<String>`으로 변경했을 뿐, 나머지 코드는 동일하다.

### **트랜잭션 관리에서 PSA 적용 (@Transactional)**

Spring에서는 `@Transactional`을 사용하여 트랜잭션을 쉽게 관리할 수 있는데 이때, 특정 구현체(JDBC, JPA, Hibernate 등)에 관계없이 트랜잭션 관리가 가능하다.

`@Transactional`을 사용하면 JDBC, JPA, Hibernate 등 다양한 트랜잭션 관리 방식에서 동일한 코드가 나온다.

**JPA → JDBC로 바꿀 때도 코드 변경이 필요하지 않고, 설정만을 바꾸면 된다.**

```java
@Service
public class AccountService {

    @Autowired
    private AccountRepository accountRepository;

    @Transactional  // 트랜잭션 관리 (PSA 적용)
    public void transferMoney(Long fromAccountId, Long toAccountId, int amount) {
        accountRepository.decreaseBalance(fromAccountId, amount);
        accountRepository.increaseBalance(toAccountId, amount);
    }
}

```

Spring Boot에서 `spring.jpa` 설정을 하면, **자동으로 `JpaTransactionManager`가 선택된다.**

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/testdb
    username: root
    password: root
  jpa:
    database-platform: org.hibernate.dialect.MySQL8Dialect
    hibernate:
      ddl-auto: update

```

JDBC를 사용할 경우 `DataSourceTransactionManager`가 적용된다.

### **캐싱에서 PSA 적용 (`@Cacheable`)**

`@Cacheable`을 사용하면 **Redis, EhCache, Caffeine 등의 캐시 기술을 쉽게 변경할 수 있다.**

```java
@Service
public class ProductService {

    @Cacheable("products")  // PSA 적용: 캐싱 기술을 변경하더라도 코드 수정 없이 사용 가능
    public Product getProductById(Long id) {
        return productRepository.findById(id).orElseThrow();
    }
}
```

**Cacheable도 설정만 바꾸면 기술 스택을 바꿀 수 있다.**
```yaml
spring:
  cache:
    type: redis
```