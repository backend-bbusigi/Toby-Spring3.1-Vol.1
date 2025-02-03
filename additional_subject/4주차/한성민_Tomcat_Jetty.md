# Tomcat, Jetty

---

Web Server는 요청을 관리하고 적절한 애플리케이션 컴포넌트에 요청을 라우팅한다.

Java Servlet, JSP, Spring을 실행하는 서블릿 컨테이너로써 두 개의 웹 서버가 널리 사용되는데 이는 톰캣(Tomcat)과 제티(Jetty)다.

## 웹서버가 필요한 이유

자바에서의 웹서버는 HTTP 요청과 응답을 주고 받는데 필수적이다.

`웹서버가 하는 일`

- 정적, 동적 페이지를 호스팅해준다.
- HTTP 요청과 응답을 다룬다.
- 웹 애플리케이션에 동시에 다수의 사용자가 접근할 때 커넥션을 관리해준다.
- 자바 서블릿을 실행함으로써, Server-Side 프로그램이다.
    - JSP의 경우, HTML과 여러 화면 요소를 사용하지만 결국 서버로의 요청이 들어와야 만들어주기 때문에(서버에서 처리) Server-Side라 할 수 있다.
- db, 미들웨어 등 접속해준다.

## 톰캣 (Tomcat)

Apache Software Foundation(ASF)에서 개발된 자바 서블릿 컨테이너로써, 오픈소스이다. 주로 자바 웹 애플리케이션의 핵심 요소인 서블릿, JSP, EL 등의 기술을 구현한다.

### 특징

1. **Java EE 사양을 준수한다.**
    - 서블릿과 JSP를 지원할 뿐만 아니라, Java 엔터프라이즈 서버 정도의 사양을 준수한다.
2. **견고하고 검증된 기술** : 오랜 기간 사용되어, 커뮤니티가 잘 되어 있다.
3. **성능 :** 소규모 및 중간 규모의 애플리케이션에서 우수한 성능을 발휘한다.
4. **엔터프라이즈 환경 지원** : 클러스터링, 로드밸런싱, 세션 유지 등의 기능을 기본적으로 제공한다. → 대규모 애플리케이션 환경에서 사용 가능
5. **관리** : 톰캣에는 관리 콘솔이 포함되어 배포, 모니터링, 구성 관리가 용이하다.

## 제티 (Jetty)

Eclipse Foundation에서 개발된 자바 서블릿 컨테이너로, 오픈소스이다. 경량급(lightweight)이고 유연한 웹 서버이다. stand-alone 혹은 자바 애플리케이션에 내장되어 동작될 수 있다.

### 특징

1. **경량급 & 유연성**
    - 톰캣과 비교하여 훨씬 가볍다.
    - 서버의 크기를 최소화한 Micro-Service, Cloud, Edge Computing 에 적합하다.
2. **모듈 설계**
    - 제티는 고도로 모듈화된 아키텍처를 갖춘다. → 필요한 구성 요소만 가져와서 사용 가능 → 메모리 및 CPU 사용량을 효율적으로 운영 가능
3. **성능**
    - 제티는 고효율적인 웹 서버로써, REST API나 WebSocket처럼 많은 커넥션을 처리할 때 뛰어나다.
4. **WebSocket 지원**
    - WebSocket 연결을 네이티브로 지원한다.
        - 톰캣에서도 WebSocket 사용 가능하다.
          하지만 제티는 기본적으로 비동기 I/O 기반이며 WebSocket 성능이 더 뛰어나다.
          또한 2번과 같이 필요 없는 기능은 제외하고 WebSocket 기능만 사용할 수 있다.
5. **동시성 및 확장성**
    - 동시 연결 처리에 최적화되어 대규모 연결을 효율적으로 처리할 수 있다.
    - 대규모 트래픽 서비스에서 사용

## 둘 중 뭘 써야 할까?

`톰캣`

- 안정적인 서블릿 컨테이너가 필요할 때
- 전통적인 웹 애플리케이션이나 표준 Java EE 스택을 따를 때
- 클러스터링, 세션 복제, 관리 기능이 내장된 서버가 필요할 때

`제티`

- 클라우드 또는 마이크로서비스 아키텍처에서 가벼운 서버가 필요할 때
- 웹 서버를 Java 애플리케이션 내부에 포함하고 싶을 때
- WebSocket 또는 API 서비스처럼 동시 연결을 많이 처리해야하는 애플리케이션을 개발할 때

## 스프링부트 웹 서버를 제티로 변경하기

스프링부트로 프로젝트를 생성하고, 별 다른 설정을 하지 않는 경우 기본 내장 서버인 톰캣을 사용한다.

![Image](https://github.com/user-attachments/assets/f7543109-8b8b-4422-8681-bfa537857ff3)

1. **톰캣 의존성을 제외한다.**

```java
implementation 'org.springframework.boot:spring-boot-starter-web' 
exclude group: 'org.springframework.boot', module: 'spring-boot-starter-tomcat'
```

`결과`
![Image](https://github.com/user-attachments/assets/c858e817-ec36-43f6-b293-efdba51ac522)

1. **Jetty 의존성 추가한다.**

```java
implementation 'org.springframework.boot:spring-boot-starter-web' 
exclude group: 'org.springframework.boot', module: 'spring-boot-starter-tomcat'

// Jetty 의존성    
implementation 'org.springframework.boot:spring-boot-starter-jetty'
```

`결과`
![Image](https://github.com/user-attachments/assets/affa62bc-43b7-4e0b-9ee6-7c1ce9ae82a6)

출처

[Tomcat vs Jetty : Choosing the right java server for your application needs.](https://www.linkedin.com/pulse/tomcat-vs-jetty-choosing-right-java-server-your-needs-asutosh-nayak-rpjff/)

[Spring Boot Tomcat을 Jetty나 UnderTow로 교체하기](https://gigyesik.tistory.com/251)

[[Spring Boot] 스프링 부트 웹 서버 톰캣(Tomcat)에서 제티(Jetty)로 변경하기](https://ittrue.tistory.com/385)
