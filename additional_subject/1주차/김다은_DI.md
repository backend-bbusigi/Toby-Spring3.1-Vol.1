## 1. DI란
- dependency injection
    - dependency(의존성) : 파라미터, 리턴값, 지역 변수 등으로 타 객체를 참조하는 것
- Bean을 관리하는 방법 중 하나
- 1장 참조

<br>

## 2. @Component
- `@Component` : 클래스에 붙이는 어노테이션으로, 해당 클래스가 스프링을 통해 빈으로 관리될 것임을 명시함
- service, repository, controller, .. 등의 어노테이션은 component annotation의 확장판

```java
@Component
public class UserService {
	...
}
```
<br>

## 3. @ComponentScan

- Spring에서 자동으로 빈을 등록하도록 설정하는 애노테이션
- `@SpringBootApplication` 에 포함됨
- 해당 애노테이션이 붙은 클래스와 그 하위 클래스를 대상으로 `@Component` 가 붙은 구성 요소를 찾아 빈으로 등록함
- xml, config 파일을 사용하지 않고도 빈을 등록할 수 있음
- 이렇게 빈으로 등록된 객체에 대해 `@Autowired`를 이용하면 스프링이 자동으로 의존성을 주입함

```java
@SpringBootApplication
@ComponentScan(basePackages = {"com.example.dev"})
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```
<br>

## 4. @Autowired

- 스프링 컨테이너에 등록된 빈에 한해 의존 관계를 설정하는 어노테이션
- 타입을 이용해 빈을 찾음
- 만약 하나의 의존 관계에 대해 여러 개의 빈을 찾으면 → 오류 발생(NoUniqueBeanDefinitionException)

1. **필드명 매칭** : 필드명을 인터페이스가 아닌 구현체의 이름으로 명시
1. `@Primary`
2. `@Qualifier`

<br>

## 5. 스프링에서의 DI

### 1. Setter

```java
@Service
public class UserService {
	private UserRepository userRepository;
	
	@Autowired
	public setUserRepository(UserRepository userRepository) {
		this.userRepository = userRepository;
	}
}
```

- 호출 될 때마다 실행되기 때문에 변수에 final X
- 안정성에 대한 위험 존재
    - public이면 외부에서 자유롭게 의존 관계 변경 가능
    - 객체 생성 후 해당 메소드를 호출하지 않을 시, NullPointerException 발생
- 주입할 대상이 없는 경우 오류 발생
    - `@Autowired(required = false)` 로 설정 가능

<br>

### 2. Constructor

```java
@Service
public class UserService {
	private final UserRepository userRepository;
	
	@Autowired
	public UserService(UserRepository userRepository) {
		this.userRepository = userRepository;
	}
}
```

- 생성자가 한 개라면, @Autowired 생략 가능
    - 생략 가능한 것이 장점인 이유 → 굳이 스프링의 의존성을 더할 이유가 없기 때문
- 생성자 호출 시 한 번만 수행됨 → 해당 변수를 final로 관리 가능
    - 대부분의 의존 관계는 변하지 않는 경우가 많아 final로 설정해도 크게 문제가 없음
    - 유일하게 객체의 생성과 의존 주입이 동시에 이뤄짐
        - 다른 방법들은 객체 생성이 먼저 이뤄지기 때문에 final X
- 파라미터 생략 시 컴파일 에러 → 잘못된 설계에 대해 바로 문제점 발견 가능
- 순수 자바 코드로 활용 가능
- 순환 참조 에러 방지 가능
- `@RequiredArgsConstructor` 롬복을 이용해 생성자 생략 가능

<br>

### 3. Field

```java
@Service
public class UserService {
	@Autowired
	private UserRepository userRepository;
}
```

- 코드가 간결함
- 해당 코드는 DI가 없으면 사용할 수 없음 → 기존 사용하던 자바 코드와는 차이가 있음
- 순환 참조 방지 불가
- 클래스 외부에서 테스트 불가
    - 테스트 코드에서 `@Autowired`를 사용하면
        
        → 단위 테스트가 아니며, 컴포넌트 초기화를 위한 시간 때문에 **테스트 비용이 증가**함
        
    - 테스트 코드에서 `@Autowired`를 사용하지 않으면
        
        → 해당 코드는 Spring 위에서 돌아가는 것이 아니므로, userRepository에 대한 **의존성이 주입되지 않아 NPE 발생**
        
<br>

### 4. Primary

```java
@Repository
@Primary
public class UserRepositoryImpl implements UserRepository {

}

@Repository
public class UserRepositoryImpl2 implements UserRepository {

} 
```

- UserRepository를 구현하는 두 개의 클래스가 빈으로 등록된다고 가정
    - UserRepository 타입의 빈이 두 개라면 ?
- `@Primary` : 해당 빈을 디폴트 빈으로 설정함
    - 의존성 주입 시 명시하지 않는다면 해당 빈을 주입하도록 설정
- 같은 타입의 multiple beans에 대해 default bean을 설정하고 싶을 때 유용

<br>

### 5. Qualifier

```java
@Service
public class UserService {

    private UserRepository userRepository;

    public MyService(@Qualifier("userRepositoryImpl2")UserRepository repository) {
        this.userRepository = repository;
    }   
}
```

```java
@Service
@RequiredArgsConstructor
public class UserService {

    @Qualifier("userRepositoryImpl2")
    private final UserRepository userRepository;
}
```

- 명시적으로 어떤 빈을 주입받을지 지정함
- 빈 이름을 명시함
    - 빈 이름 : 클래스 이름 + 첫 글자 lower
- 다른 빈을 동일한 injection poin에서 사용해야 할 때 유용
- primary, qualifier가 같이 존재할 때는, Qualifier가 우선시 됨

<br>

> 💡침투적인 코드
>
> ---
> - 스프링 프레임워크의 의존성을 최대한 줄이는 것이 깔끔한 코드
> - `@Autowired`의 경우, 스프링 의존성이 해당 서비스에 침투하게 됨
>
> → 줄일 수 있으면 줄이는 것이 좋으며 최대한 순수 자바 코드로 작성하는 것을 습관 들이는 것이 좋음

<br>

## 📚 참고자료
- https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html
- https://engineerinsight.tistory.com/46
- https://mininkorea.tistory.com/48
- https://mangkyu.tistory.com/125
- https://sjh9708.tistory.com/136
- https://velog.io/@neity16/Spring-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8-8-Primary-Qualifier
- https://moonong.tistory.com/96
- https://bestinu.tistory.com/58