# IoC (Inversion of Control)

스프링에서 new로 오브젝트를 생성하여 개발자가 관리하는 것이 아닌, 스프링 컨테이너에 맡기는 것을 의미한다.

즉, 개발자 → 프레임워크로 오브젝트를 관리하는 권한이 넘어갔음으로 **제어의 역전**이라 한다.

## IoC 예시

```java
@RestController
public class ReadActivityCalendarController implements ReadActivityCalendarSwagger {

    private final ActivityDao activityDao;

    public ReadActivityCalendarController() {
        ActivityDao activityDaoImpl = new ActivityDaoImpl();
        this.activityDao = new ActivityDao(activityDaoImpl);
    }
    ...
}
```

```java
@RestController
public class ReadActivityCalendarController implements ReadActivityCalendarSwagger {

    private final ActivityDao activityDao;

    public ReadActivityCalendarController(ActivityDao activityDao) {
        this.activityDao = activityDao;
    }
    ...
}
```

`ReadActivityCalendarController`는 `ActivityDao`를 사용하는 클래스이고, ActivityDao의 구현체는 `ActivityDaoImpl`이다.

- 첫 번째 예제에서는 `ActivityDao`를 직접 `new` 키워드를 사용하여 생성한다.
    - 이 방식은 `ReadActivityCalendarController`가 본연의 책임 이외의 `ActivityDao`의 생성 책임을 가지게 되어, 객체 간의 결합도가 높아지고, `ActivityDao`의 구현체가 다양해지면 개발자(혹은 고객)가 매번 구현체를 변경해야하는 문제가 발생한다.
- 두 번째 예제에서는 생성자의 매개변수를 통해 `ActivityDao`를 외부에서 주입받고 있다.
    - 이러한 방식을 의존성 주입(DI, Dependency Injection)이라고 하며, 객체의 생성과 제어 권한을 외부로 넘기고 있다.

이처럼 제어의 주도권이 객체 내부에서 외부(IoC 컨테이너)로 넘어가는 것을 제어의 역전(IoC, Inversion of Control)이라 한다.

# IoC 컨테이너

스프링에서 빈(Bean)을 생성하고 관계를 설정하며 제공(주입)하는 역할을 IoC 컨테이너가 담당한다. `BeanFactory`는 이러한 IoC 컨테이너의 기본 역할을 수행하며, `ApplicationContext`는 `BeanFactory`를 상속받아 동일한 기능을 제공하면서도 다양한 인터페이스를 추가로 상속받아 더 많은 기능을 제공한다. 이러한 확장된 기능 덕분에 `ApplicationContext`가 일반적으로 더 많이 사용된다.

`ApplicationContext`

![1](https://github.com/user-attachments/assets/eb8d101f-50c8-4f61-a6d9-0a5dc3562669)

`BeanFactory를 상속받는 ListableBeanFactory`

![2](https://github.com/user-attachments/assets/743aa4ff-088f-456a-a4d6-6c4707267071)

`BeanFactory를 상속받는 HierarchicalBeanFactory`

![3](https://github.com/user-attachments/assets/38991359-d747-4979-a546-29960956d350)

- 참고) `ApplicationContext`가 제공하는 기능
    - **Bean factory methods for accessing application components. Inherited from ListableBeanFactory.**
        - 애플리케이션 구성 요소에 접근하기 위한 Bean 팩토리 메서드. `ListableBeanFactory`로부터 상속됨.
    - **The ability to load file resources in a generic fashion. Inherited from the org.springframework.core.io.ResourceLoader interface.**
        - 파일 리소스를 일반적인 방식으로 로드하는 기능. `org.springframework.core.io.ResourceLoader` 인터페이스로부터 상속됨.
    - **The ability to publish events to registered listeners. Inherited from the ApplicationEventPublisher interface.**
        - 등록된 리스너에게 이벤트를 발행하는 기능. `ApplicationEventPublisher` 인터페이스로부터 상속됨.
    - **The ability to resolve messages, supporting internationalization. Inherited from the MessageSource interface.**
        - 메시지를 해석하는 기능과 국제화 지원. `MessageSource` 인터페이스로부터 상속됨.
    - **Inheritance from a parent context. Definitions in a descendant context will always take priority. This means, for example, that a single parent context can be used by an entire web application, while each servlet has its own child context that is independent of that of any other servlet.**
        - 부모 컨텍스트로부터 상속. 자식 컨텍스트의 정의는 항상 우선권을 가짐. 예를 들어, 단일 부모 컨텍스트는 전체 웹 애플리케이션에서 사용될 수 있으며, 각 서블릿은 다른 서블릿과 독립적인 고유의 자식 컨텍스트를 가질 수 있음.

# 실습

## 요구사항

- 두 가지 구현체를 만들어 각기 다른 방식으로 알림을 전송한다.
    1. **EmailNotification**: 이메일로 알림 전송.
    2. **SmsNotification**: SMS로 알림 전송.
- 인터페이스를 통해 다양한 알림 방식을 쉽게 확장할 수 있도록 설계한다.

---

## 구현 세부 사항

1. **인터페이스 정의**
    - `NotificationService` 인터페이스를 생성해 알림 전송 기능의 표준 메소드를 정의한다.
    - 메소드는 `sendNotification()` 1개만 존재한다.

2. **구현체 작성**
    - `EmailNotificationService`: 이메일 방식으로 알림을 전송한다.
        - `System.out.println("Send Email Notification");` 을 출력하는 기능
    - `SmsNotificationService`: SMS 방식으로 알림을 전송한다.
        - `System.out.println("Send SMS Notification");` 을 출력하는 기능

3. **컨트롤러에서 DI를 통해 구현체를 주입받아 사용**
    - IoC 컨테이너(Spring)를 활용해 구현체를 생성 및 관리한다.
    - 특정 상황에 따라 이메일 또는 SMS 방식을 선택한다.

![4](https://github.com/user-attachments/assets/09dbf211-68d8-44a2-929a-ff975628a177)

---

`IoC, DI 적용 X`

### **커밋 1: 인터페이스와 구현 클래스 생성 및 main()에서 직접 구현체 사용하여 출력**

- **작업 내용**:
    - `NotificationService` 인터페이스 생성.
    - `EmailNotificationService`와 `SmsNotificationService` 클래스에서 인터페이스 구현.
    - `main()` 메서드에서 `NotificationService` 구현체를 각각 생성 및 사용.

```text
출력 예시
Send Email Notification
Send SMS Notification
```

### **커밋 2: Notification 클래스 생성 후 변수 주입 및** main에서 `Notification`을 사용해서 출력하기

- **작업 내용**:
    - `Notification` 클래스에 `NotificationService`를 인스턴스 변수로 추가.
    - 생성자에서 `NotificationService`를`EmailNotificationService`를 new로 생성하여 주입
    - `NotificationService` 의 메소드를 사용하는 `send()` 메소드 추가

```text
출력 예시
Send Email Notification
```

`DI 적용 O`

### **커밋 3: 구현체를 외부에서 주입**

- **작업 내용**:
    - `Notification` 클래스에서 `NotificationService`를 생성자 매개변수로 받아 의존성을 외부에서 주입.
    - `SmsNotificationService`를 main()에서 생성자에 주입

```text
출력 예시
Send SMS Notification
```

`IoC 적용 O` - 여기서부터 Spring Framework 사용

### **커밋 4: IoC 적용 - Spring Framework 사용**

- **작업 내용**:
    - `@Component`를 사용해 `NotificationService`와 `두 개의 구현체`를 스프링 빈으로 등록.
    - `ApplicationContext`를 이용해 `EmailNotificationService`를 가져와 `Notification` 생성 및 사용.
    - `Notification`은 빈으로 등록 X

```text
출력 예시
Send Email Notification
```

### **커밋 5: 수정자 주입으로 DI 리팩토링**

- **작업 내용**:
    - 커밋 4에서 `EmailNotificationService`는 생성자 주입으로, `SmsNotificationService`는 수정자(setter) 주입으로 변경하여 출력.

### **커밋 6: 다수의 구현체를 처리하기 위한 @Qualifier 또는 @Primary 추가**

- **작업 내용**:
    - `Notification` 빈 등록.
    - `EmailNotificationService` 또는 `SmsNotificationService`에 `@Primary` 또는 `@Qualifier`를 사용해 우선순위 지정.
    - 스프링 컨텍스트에서 충돌을 해결.
    - `Notification`을 컨텍스트에서 찾아와서 출력하기 (구현체는 가져오지 말 것!)

### **커밋 7: @Bean으로 직접 빈 생성 및 컨텍스트에서 조회**

- **작업 내용**:
    - `@Bean`을 이용해 `NotificationService`의 구현체를 수동으로 등록. (`EmailNotificationService`, `SmsNotificationService` 둘 다 만들기)
    - `ApplicationContext`에서 `getBean()`으로 빈 조회.
