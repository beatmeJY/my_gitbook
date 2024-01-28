---
description: 🍇🍈🍉🍊🍋🍌🍑🍐🍏🍎🥭🍍🍒🍓🥝🥑
---

# 🌻 Spring

## 기타

### 🍇 스프링 vs 스프링부트

* 스프링부트는 스프링을 보다 더 쉽게 개발 할 수 있도록 사용합니다.
* 주로 XML 등의 기본설정들을 자동으로 해주고 ,
* 톰캣과 같은 내장 서버를 제공하여 외부 서버 설정이 필요 하지 않으며,
* 의존성을 미리 정의해두어 복잡한 의존성을 더 편리하게 추가 할 수 있어 개발자가 개발 이외에 시간에 소비하는 것을 단축 시켜줍니다.



### 🍈 스프링부트 3.x버전을 사용한 이유

* SpringBoot 2.0은 23년 11월까지만 공식지원이 되어 이제 지원은 종료되었기 때문에 새로운 버전에 대한 학습이 필요하다고 판단을 했습니다.
* 또한 스프링부트 3.x부터는 Java17을 기본으로 채택하고 있기 때문에 Java17을 추가로 학습하며 진행 할 수 있습니다.
* 스프링부트 3.x는 메트릭, 로깅, 분산 추적 등의 기능을 제공하는 [Observability](https://spring.io/blog/2022/10/12/observability-with-spring-boot-3)을 일반적으로 사용 가능하도록 공식 지원 합니다.
* 추가적으로 아래 링크들을 통해 다른 이슈들에 대해서도 확인해 볼 수 있습니다.&#x20;
  * [Release Note](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Release-Notes)
  * [Tracing 관련기능](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#actuator.micrometer-tracing)

🍀 위와 같은 이유들로 스프링 부트 3.0을 사용하게 되었습니다.



### 🍉 라이브러리 vs 프레임워크



|       | 라이브러리                      | 프레임워크                |
| ----- | -------------------------- | -------------------- |
| 사용 방식 | 개발자가 필요에 따라 라이브러리 함수 직접 호출 | 프레임워크에 정의된 규칙에 따라 작성 |
| 제어 흐름 | 주로 개발자가 제어                 | 주로 프레임워크가 제어         |
| 코드 구성 | 라이브러리는 일반적으로 독립적으로 사용      | 애플리케이션의 구조를 정의       |
| 예시    | JQuery, Thymeleaf 등        | Spring, Django 등     |

###

### 🍊 WAS vs Web Server

#### Web Server

* 웹 서버의 주된 기능은 정적 웹 페이지(콘텐츠)를 클라이언트로 전달하는 것입니다.&#x20;
* 주로 그림, CSS, 자바스크립트를 포함한 HTML 문서가 클라이언트로 전달됩니다.&#x20;
* HTTP 서버로 URL과 HTTP의 소프트웨어 일부입니다.

#### WAS (Web Application Sever)

* 주로 Web Server의 기능을 포함한 동적인 처리를 주로 담당합니다.
* 프로그램 실행 환경과 데이터베이스 접속 기능을 제공하고 여러 개의 트랜잭션을 관리하며 업무를 처리하는 비즈니스 로직을 수행합니다.
* 이를통해 DB 조회나 로직 처리를 요구하는 동적 컨텐츠를 처리하고 응답하여 줄 수 있습니다.
* WAS의 대부분은 Java EE 표준을 준수하여 만들어집니다.



## 동작원리

### 🍋 Dispatcher Servlet 동작과정

<figure><img src=".gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

1. **HTTP 요청**:&#x20;
   * 클라이언트로부터 HTTP 요청을 받습니다.
2. **Handler(컨트롤러) 조회**:&#x20;
   * Dispatcher Servlet이 응답을 받아 Handler Mapping을 통해 HTTP요청 URL(헤더라던지 다른정보 더 확인)에 매핑된 Handler 조회합니다.
3. **핸들러 어댑터 조회**:&#x20;
   * Dispatcher Servlet이 조회한 핸들러를 처리할 수 있는 Handler Adapter 조회합니다.
4. **핸들러 어댑터 실행**:&#x20;
   * 조회 된 Handler Adapter가 Handler를 실행합니다.
5. **ModelAndView 반환**:&#x20;
   * Handler 실행 후 반환된 정보를 Handler Adapter가 ModelAndView로 변환해서 반환합니다.
6. **viewResolver 호출**:&#x20;
   * Dispatcher Servlet은 ViewResolver를 찾아 실행합니다.
7. **View 반환** :&#x20;
   * ViewResolver는 View의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 View 객체를 반환합니다.
8. **View 렌더링**: View를 통해서 View를 렌더링 합니다.
   * 다른 View는 실제 View를 렌더링 하지만 JSP의 경우 'forward()'를 통해서 해당 JSP로 이동해야 렌더링이 됩니다.



### 🍑 Dispatcher Servlet는 동기 vs 비동기?

* 간단한 동작 환경에선 별도의 설정 없이 **동기로 처리하는 것이 일반적**입니다.
* 긴 응답이 필요하거나 높은 성능이 필요한 상황에서는 **비동기로 처리하여 효율**을 낼 수 있습니다.
* 하지만 스프링 MVC의 비동기 지원 기능 (`DeferredResult`, `Callable`, `CompletableFuture)`을 이용하면 비동기로 처리할 수도 있습니다.
* 또는 @Async를 통해 메서드를 비동기적으로 처리할 수도 있습니다.
* 따라서 개발자의 설정에 따라 동기와 비동기를 모두 사용할 수 있으므로 상황에 대한 판단이 필요합니다.



### 🍐 톰캣의 동작 과정

<figure><img src=".gitbook/assets/image (65).png" alt=""><figcaption><p><a href="https://velog.io/@jihoson94/Servlet-Container-%EC%A0%95%EB%A6%AC">https://velog.io/@jihoson94/Servlet-Container-%EC%A0%95%EB%A6%AC</a></p></figcaption></figure>

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



## DI(Dependency Injection)

### 🍏 Autowired vs Resources vs Inject

#### Autowired

* 스프링 프레임워크에서 지원 되며 타입에 따라 자동으로 빈을 주입하는데 사용이 됩니다.
* 주로 필드, 메서드, 생성자에 적용됩니다.
* 찾는 순서 (타입 -> 이름 -> @Qualifier -> 실패)

#### Resources

* Java EE의 표준 어노테이션으로 이름에 따라 의존성을 주입하는데 사용됩니다.
* 필드, 세터, 메서드 및 생성자 매개변수에 적용할 수 있습니다.
* 찾는 순서 (이름 -> 타입 -> @Qualifier -> 실패)

#### Inject

* Java의 표준 어노테이션으로 타입에 따라 주입 할 수 있습니다.
* 주로 필드 및 생성자에 적용됩니다.
* 찾는 순서 (타입 -> @Qualifier-> 이름 -> 실패)



### 🥝  @Qualifier란

* 만약에 타입이 동일한 bean객체가 여러개 있으면 Spring이 Exception을 일으킵니다.
* 때문에Spring 에서 여러 빈 중 어떤 빈을 주입할지를 지정하기 위한 어노테이션입니다.

```null
pubic class MemberDao{  
    @Autowired  @Qualifier("m1")
    private Member member;       

    public void setMember(Member member){      
        this.member = member;  
    }
}
```



### 🍎 Spring의 DI란?

* 의존성 주입이라고 부르며 객체가 실제로 필요한 의존성을 직접 생성하는 것이 아니라 외부에서 주입받도록 하는 것을 의미합니다.



### 🥭 DI의 장점은 무엇이 있을까요

* 새로운 구현체로 교체하거나 주입 받는 대상을 변경하더라도 구현 자체를 수정하지 않아도 됩니다.
* 가짜 Mock 객체를 사용하여 테스트하기에 좋은 유연한 코드를 만들 수 있습니다.
* 생성자 등 파라미터에 의존성이 명시적으로 드러나 가독성을 향상 시킬 수 있습니다.



### 🍍 DI의 3가지 방법에 대해 설명해주세요

#### 생성자 주입

```
public class Car { 
    private Engine engine;
    public Car(Engine engine) {
        this.engine = engine;
    }
}
```

* 의존성을 객체의 생성자를 통해 주입하는 방법입니다.
* 객체를 생성할 때 필요한 의존성을 외부에서 전달 받도록 하는 것입니다.

#### 수정자(Setter) 주입

```
public class Car {
    private Engine engine;
    public void setEngine(Engine engine) {
        this.engine = engine;
    }
}
```

* setter 메서드와 같은 수정자를 통해 주입하는 방법입니다.
* 주로 의존관계가 변경 가능성이 있는 상황에서 사용됩니다. (final 키워드 사용 불가)

#### 필드주입

```
public class car { 
    @Autowired
    private Engine engine;
}
```

* 프레임워크나 라이브러리를 통해 주입하는 방법입니다.
* 테스트 하기에 어려움이 있어 권장되지 않는 방법입니다.
* 실제 코드와는 무관한 테스트코드 등의 간단한 로직에서만 사용하는 것이 좋습니다.



### 🍒 IOC(Inversion Of Control)에 대해 설명해주세요

* 제어의 역전이라고 불리며 스프링에서도 채택되어 사용되고 있는 소프트웨어 디자인패턴 중 하나입니다.
* 주로 의존성 주입(DI)와 함께 사용되며객체의 생성, 생명 주기, 의존성 관리 등을 개발자가 아닌 외부에 위임하는 것을 목적으로 합니다.



## 테스트

### 🍓 목 객체를 사용하는 이유가 무엇일까요?

* 주로 단위 테스트를 하기 위해서 사용합니다.
* 테스트 시에 주로 다른 특정 클래스나 모듈이 다른 클래스나 모듈에 의존하게 됩니다.
* 이러한 의존성을 해결하기 위해서 목 객체를 사용하여 코드의 의존성을 분리하여 격리된 테스트 환경을 구축 할 수 있습니다.
* 특정 메소드 호출이나 반환 값을 지정하여 예외를 발생 시킬 수 도 있어 원하는 상황을 재현하고 특정 코드 경로를 테스트 할 수 도 있습니다.
* 외부 서비스와의 테스트 시에도 호출 하지 않고 테스트를 할 수 있습니다.

