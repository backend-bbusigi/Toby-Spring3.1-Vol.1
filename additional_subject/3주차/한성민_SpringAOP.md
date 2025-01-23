# Spring AOP

---

## AOP란?
![Image](https://github.com/user-attachments/assets/3ea701d7-fdf7-4fbb-bd35-bc590b44a068)

- 관점 지향 프로그래밍
- 횡단 관심사(Cross-Cutting Concern)의 분리를 허용함으로써 모듈성을 증가시키는 것이 목적인 프로그래밍 패러다임

```text
만약 실행 시간의 Log를 하나의 API라면 로직 내에 넣을 수 있겠지만, 
수백/수만 개의 API라면 로직 내에 반복/중복 작업이 발생하는데 이 문제를 해결하고 핵심 기능 개발에만 집중할 수 있게 해준다.

→ 원하는 곳에 공통 관심 사항을 적용
```

## Spring AOP 주요 애노테이션
![Image](https://github.com/user-attachments/assets/33e79ca5-4a2e-4962-a89c-38d0a4a9bd5b)

**추가**

- @Aspect
    - 자바에서 널리 사용되는 AOP 프레임워크에 해당하며, AOP를 정의하는 클래스에 해당
- @Pointcut
    - 기능을 어디에 적용시킬 지점을 결정 (클래스, 메소드, 애노테이션)
---

## 실습 (AOP를 이용하여 로그 출력하기)

- 모든 API의 파라미터 타입과 값을 로그로 출력하려 한다.
- 중복 코드를 제거하고 AOP를 활용해 모든 API에서 동작하도록 한다.
- 매개변수로 들어오는 값과 메소드 종료 후 반환돠는 값을 로그로 출력하도록 한다.

`ParameterAop`

`execution`을 사용하여 정의된 패키지 하위에서 실행
```java
@Aspect // AOP(Aspect Oriented Programming)를 적용하기 위한 클래스임을 나타냄
@Component // 해당 클래스가 Spring 컨텍스트에 Bean으로 등록되도록 지정
@Log4j2 // Log4j2를 사용하기 위한 어노테이션
public class ParameterAop {

  // controller 하위의 모든 메서드에 대해 Pointcut을 정의
  @Pointcut("execution(* com.example.backend.controller..*.*(..))")
  private void pointCut() {} // 이 메서드는 Pointcut의 이름을 정의하기 위한 빈 메서드

  // Pointcut에 정의된 메서드 실행 이전에 실행
  @Before("pointCut()")
  public void before(JoinPoint joinPoint) {
    MethodSignature methodSignature = (MethodSignature) joinPoint.getSignature();
    Method method = methodSignature.getMethod();
    // 현재 실행 중인 메서드의 정보를 Method 객체로 가져옴
    log.info("Executing method: {}", method.getName());
    // 실행 중인 메서드 이름을 로그로 출력

    // 메서드 매개변수들을 가져옴
    Object[] args = joinPoint.getArgs();
    for (Object obj : args) {
      log.info("Parameter type: {}, value: {}", obj.getClass().getSimpleName(), obj);
      // 매개변수의 타입과 값을 로그로 출력
    }
  }

  // Pointcut에 정의된 메서드 실행 후, 결과 값을 반환할 때 실행
  @AfterReturning(value = "pointCut()", returning = "returning")
  public void afterReturn(JoinPoint joinPoint, Object returning) {
    log.info("Returned object: {}", returning);
    // 반환된 객체를 로그로 출력
  }
}
```

`annotation`을 사용하여 annotation을 설정한 곳에서 실행
```java
@Aspect // AOP(Aspect Oriented Programming)를 적용하기 위한 클래스임을 나타냄
@Component // 해당 클래스가 Spring 컨텍스트에 Bean으로 등록되도록 지정
@Log4j2 // Log4j2를 사용하기 위한 어노테이션
public class ParameterAop {

  // @LogExecution 어노테이션이 붙은 메서드에 적용
  @Around("@annotation(com.example.backend.aop.LogExecution)")
  public Object logExecution(ProceedingJoinPoint joinPoint) throws Throwable {
    // 메서드 실행 전 로깅
    String methodName = joinPoint.getSignature().toShortString();
    log.info("Executing method: {}", methodName);

    Object[] args = joinPoint.getArgs();
    for (Object arg : args) {
      log.info("Parameter type: {}, value: {}", arg.getClass().getSimpleName(), arg);
    }

    // 메서드 실행
    Object result;
    try {
      result = joinPoint.proceed();
    } catch (Throwable throwable) {
      log.error("Exception occurred in method: {}", methodName, throwable);
      throw throwable;
    }

    // 메서드 실행 후 로깅
    log.info("Method executed: {}, Returned value: {}", methodName, result);
    return result;
  }
}

// 커스텀 어노테이션 정의
@Target(ElementType.METHOD) // 메서드에만 적용 가능
@Retention(RetentionPolicy.RUNTIME) // 런타임에 유지됨
public @interface LogExecution {
}
```
-> `@Before`, `@After`대신 `@Around` 를 사용할 수 있다.
