# 1. 서블릿
## 1.1 서블릿의 정의

- 클라이언트의 요청을 처리하기 위한 자바 프로그램
- 웹서버가 동적으로 페이지를 제공할 수 있도록 도와주는 어플리케이션

<br>

## 1.2 서블릿의 특징

- 클라이언트의 요청에 대해 동적으로 작동
- MVC 패턴에서 컨트롤러로 이용됨
- HTML을 이용함
    - 자바 소스 코드 속에 HTML이 들어감
- Java의 Thread를 이용함
- UDP보다 처리 속도가 느림
- HTTP 프로토콜을 사용함
    - `javax.servlet.http.HttpServlet`클래스를 상속

데이터베이스 연결, HTML 폼을 이용한 사용자 데이터 받기, 동적 웹 환경 구성 등의 기능들은 서블릿이 있기 때문에 구현이 가능한 것임

<br>

## 1.3 서블릿의 동작 과정

1. 클라이언트가 HTTP를 이용해 Request를 보냄
2. Request가 Servlet Container로 전송됨
3. Servlet Container은 `HttpServletRequest`, `HttpServletResponse` 객체를 생성함
4. `web.xml` 혹은 `@WebServlet`을 기반으로 어느 서블릿에 대한 요청인지 파악함
5. 해당 서블릿에서 `service` 메소드 호출함
6. 클라이언트가 요청한 HTTP Method에 따라 `doGet()` 혹은 `doPost()`을 호출함
    - 모든 요청은 `service()`를 통해 `doXX()`으로 연결됨
7. 메소드는 동적 페이지를 생성하고 `HttpServletResponse`에게 보냄
8. `HttpServletRequest`, `HttpServletResponse` 객체를 소멸시킴

```java
@WebServlet("/")
public class Servlet extends HttpServlet {

	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
	}
}
```
<br>

## 1.4 서블릿의 생명주기

1. 클라이언트의 요청이 들어오면 컨테이너는 해당 서블릿이 메모리를에 있는지 확인함
    - 없다면 `init()`을 호출해 인스턴스를 생성함
    - 해당 메소드는 처음 한 번만 호출됨
    - 실행 중 서블릿이 변경될 경우 기존 서블릿을 파괴하고 메소드를 호출해 초기화함
2. 클라이언트의 요청에 따라 `service()` 메소드를 수행함
3. 컨테이너가 서블릿에 종료 요청을 보내면 `destory()` 메소드를 호출해 종료함
    - 최초 요청 시 한 번만 호출됨

![image.png](attachment:9de6eb18-de19-448a-849c-d77dc05e5eb1:image.png)

<br><br>

# 2. 서블릿 컨테이너
## 2.1 서블릿 컨테이너의 정의

- 서블릿을 관리해주는 컨테이너
- 클라이언트의 요청을 받고 응답하기 위해 웹서버와 웹소켓으로 통신함
- WAS의 일종

<br>

## 2.2 서블릿 컨테이너의 특징

- 요청을 받으면 적절한 서블릿에게 요청에 대한 처리를 위임함
- 웹 서버와의 통신 지원
    - 서블릿-웹서버 간의 통신이 원활하도록 도움
    - 일반적인 웹소켓을 이용한 통신은 accept, listen, .. 등이 필요하나, 서블릿 컨테이너는 이러한 웹소켓과의 통신을 API로 제공함
- 서블릿 생명주기 관리
- 멀티쓰레드 지원
    - 요청이 올 때마다 자바 Thread를 생성하고 요청이 종료되면 Thread를 종료시킴