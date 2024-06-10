# Spring Web

## WAS vs Web Server

#### Web Server

* 웹 서버의 주된 기능은 정적 웹 페이지(콘텐츠)를 클라이언트로 전달하는 것입니다.&#x20;
* 주로 그림, CSS, 자바스크립트를 포함한 HTML 문서가 클라이언트로 전달됩니다.&#x20;
* HTTP 서버로 URL과 HTTP의 소프트웨어 일부입니다.

#### WAS (Web Application Sever)

* 주로 Web Server의 기능을 포함한 동적인 처리를 주로 담당합니다.
* 프로그램 실행 환경과 데이터베이스 접속 기능을 제공하고 여러 개의 트랜잭션을 관리하며 업무를 처리하는 비즈니스 로직을 수행합니다.
* 이를통해 DB 조회나 로직 처리를 요구하는 동적 컨텐츠를 처리하고 응답하여 줄 수 있습니다.
* WAS의 대부분은 Java EE 표준을 준수하여 만들어집니다.



## Tomcat 이란?

***

> 웹 어플리케이션 서버(WAS)로 웹 서버의 기능을 포함하고 있는 서블릿 컨테이너입니다.

### Tomcat의 동작 과정

<figure><img src="../../.gitbook/assets/image (65).png" alt=""><figcaption><p><a href="https://velog.io/@jihoson94/Servlet-Container-%EC%A0%95%EB%A6%AC">https://velog.io/@jihoson94/Servlet-Container-%EC%A0%95%EB%A6%AC</a></p></figcaption></figure>

1. **서버 시작:**
   * 톰캣이 시작되면 서버 인스턴스가 생성됩니다.
   * 서버 인스턴스는 서버 소켓을 열고 클라이언트의 요청을 대기합니다.
2. **웹 어플리케이션 로딩:**
   * 톰캣은 배포된 웹 어플리케이션의 설정을 읽고 필요한 클래스들을 메모리에 로드합니다.
   * 서블릿 컨테이너는 서블릿 및 필터 등을 초기화하고 인스턴스화합니다.
3. **HTTP 요청 수신:**
   * 클라이언트의 HTTP 요청이 도착하면 서버 소켓(Connector)은 이 요청을 받아들이고 요청을 처리할 스레드를 생성합니다.
   * 서블릿 컨테이너는 해당 요청을 분석하고 HttpServletRequest , HttpServletResponse 두 객체를 생성합니다.
4. **서블릿 처리:**
   * 서블릿 컨테이너는 해당 요청을 처리할 서블릿을 결정합니다.
   * 해당 서블릿이 처음 요청되어 메모리에 없으면 인스턴스 생성 및 초기화를 하고, 이미 해당 서블릿의 인스턴스가 메모리에 있다면 기존 인스턴스를 재사용합니다.
   * 서블릿의 service() 메서드가 호출되어 POST , GET 여부에 따라 doGet() 또는 doPost() 등의 요청을 처리하고 HttpServletResponse에 응답을 생성합니다.
   * 필요에 따라 서블릿은 데이터베이스와 같은 외부 리소스와 상호 작용할 수 있습니다.
5. **HTTP 응답 전송:**
   * 서블릿이 생성한 응답은 다시 톰캣 서버를 통해 클라이언트에 전송됩니다.
   * 응답은 HTTP 응답 헤더 및 본문을 포함하며, 클라이언트에게 표시되거나 처리됩니다.
   * 응답 완료 시 HttpServletRequest, HttpServletResponse 두 객체를 소멸합니다.
6. **서버 종료:**
   * 톰캣 서버가 중지되거나 종료될 때, 서버는 현재 실행 중인 웹 어플리케이션들을 정리하고destroy() 자원을 해제합니다. ( destroy() )



## Dispatcher Servlet 이란?

***

> HTTP 프로토콜로 들어오는 모든 요청을 받아 가장 적합한 컨트롤러에게 위임해주는 프론트 컨트롤러 라고 볼 수 있습니다.

### Dispatcher Servlet의 동작 과정

<figure><img src="../../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

1. **HTTP 요청**:&#x20;
   * 클라이언트로부터 HTTP 요청을 받습니다.
   * 이 때, PreHandle이 호출됩니다.
2. **핸들러 (컨트롤러) 조회**:&#x20;
   * Dispatcher Servlet이 응답을 받아 핸들러 매핑을 통해
   * HTTP요청 URL  (헤더 등 다른 정보들 확인)에 매핑 된 핸들러를 조회합니다.
3. **핸들러 어댑터 조회 및 실행**:&#x20;
   * Dispatcher Servlet이 조회한 핸들러를 처리할 수 있는 핸들러 어댑터를 조회합니다.
   * 조회 된 핸들러 어댑터가 핸들러를 실행합니다.
4. **ModelAndView 반환**:&#x20;
   * 핸들러실행 후 반환 된 정보를 핸들러 어댑터가 ModelAndView 로 변환해서 반환합니다.
   * 이후 PostHandle이 호출됩니다.
5. **뷰 리졸버 호출**:&#x20;
   * Dispatcher Servlet은 뷰 리졸버를 찾아 실행합니다.
6. **뷰 반환** :&#x20;
   * 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환합니다.
7. **뷰 렌더링**:&#x20;
   * 뷰를 렌더링하기 전 afterCompletion이 호출됩니다.
   * 이후 뷰를 통해 뷰를 렌더링 합니다.
   * JSP의 경우 'forward()'를 통해서 해당 JSP로 이동해야 렌더링 됩니다.



### Dispatcher Servlet은 동기 vs 비동기?

* 간단한 동작 환경에선 별도의 설정 없이 **동기로 처리하는 것이 일반적**입니다.
* 긴 응답이 필요하거나 높은 성능이 필요한 상황에서는 **비동기로 처리하여 효율**을 낼 수 있습니다.
* 스프링 MVC의 비동기 지원 기능 (`DeferredResult`, `Callable`, `CompletableFuture)`을 이용하면 비동기로 처리할 수도 있습니다.
* 또는 `@Async`를 통해 메서드를 비동기적으로 처리할 수도 있습니다.
* 따라서 개발자의 설정에 따라 동기와 비동기를 모두 사용할 수 있으므로 `상황에 대한 판단`이 필요합니다.







