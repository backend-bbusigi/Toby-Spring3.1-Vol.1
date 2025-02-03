# 3. 스프링 MVC

## 3.1 스프링 MVC?

- 서블릿을 기반으로 만들어진 웹 프레임워크
- 모델 + 뷰 + 컨트롤러
- MVC의 등장 배경
    - 스프링은 클라이언트의 요청을 받아 HTML으로 응답을 반환했음
    - 비즈니스 로직과 뷰 로직이 한 군데에 엉켜 있어 유지보수 측면에서 취약했음
    - 이러한 엉킨 코드들을 분리하고 중복 코드를 줄이고자 패턴을 도입함

<br>

## 3.2 스프링 MVC의 구조

- Model
    - 클라이언트의 요청 사항을 작업한 결과 데이터
- View
    - 애플리케이션 화면에 보이는 리소스
    - PDF, HTML, Excel, XML, JSON 등의 리소스를 지원함
- Controller
    - 클라이언트의 요청을 받는 엔드포인트
    - Model - View 간의 상호작용을 담당함

<br>

## 3.3 DispatcherServlet

- SpringBoot의 경우 DispatcherServlet + Controller 이 서블릿의 역할을 대신 함
    - DispatcherServlet → FrameworkServlet → HttpServletBean → HttpServlet
- **FrontController**
    - 모든 클라이언트의 요청을 앞 단에서 받아 적합한 Controller에게 위임하는 컨트롤러
    - **DispatcherServlet**이 이 역할을 함
    - 이로 인해 개발자가 각각 서블릿을 만들고 URL을 매핑하는 귀찮음이 줄어들었음

<br>

## 3.4 스프링 MVC 동작 과정

![mvc.png](attachment:f8142086-85ec-4d4d-b12a-c28ea608eec9:mvc.png)

1. 클라이언트가 Request 요청을 보내면 WAS가 HTTP 요청을 파싱함
2. 정적 페이지를 처리한 후 Request, Response에 대해 Filter을 수행함
3. `HttpServletRequest`, `HttpServletResponse` 객체를 만들어 `DispatcherServle`t에게 전달함
4. DispatcherServlet의 구현체인 `HandlerMapping`을 통해 요청에 대한 매핑 정보를 확인하고 알맞는 핸들러를 조회함
5. `HandlerAdaptor`을 통해 핸들러를 실행할 수 있는 어댑터를 조회함
6. HandlerAdaptor을 실행해 `handler`, 즉 Controller를 호출함
7. 각 Controller는 요구된 데이터를 반환하고 HandlerAdaptor는 데이터를 `ModelAndView`로 변환해 반환함
8. DispatcherServlet은 `ViewResolver`을 실행함
9. ViewResolver는 View의 이름을 통해 `View`객체를 반환함
10. View 객체를 이용해 렌더링해 응답을 반환함