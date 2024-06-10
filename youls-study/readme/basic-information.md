# Basic Information

### 스프링 vs 스프링부트

* 스프링부트는 스프링을 보다 더 쉽게 개발 할 수 있도록 사용합니다.
* 주로 XML 등의 기본설정들을 자동으로 해주고 ,
* 톰캣과 같은 내장 서버를 제공하여 외부 서버 설정이 필요 하지 않으며,
* 의존성을 미리 정의해두어 복잡한 의존성을 더 편리하게 추가 할 수 있어 개발자가 개발 이외에 시간에 소비하는 것을 단축 시켜줍니다.



### 스프링부트 3.x버전을 사용한 이유

* SpringBoot 2.0은 23년 11월까지만 공식지원이 되어 이제 지원은 종료되었기 때문에 새로운 버전에 대한 학습이 필요하다고 판단을 했습니다.
* 또한 스프링부트 3.x부터는 Java17을 기본으로 채택하고 있기 때문에 Java17을 추가로 학습하며 진행 할 수 있습니다.
* 스프링부트 3.x는 메트릭, 로깅, 분산 추적 등의 기능을 제공하는 [Observability](https://spring.io/blog/2022/10/12/observability-with-spring-boot-3)을 일반적으로 사용 가능하도록 공식 지원 합니다.
* 추가적으로 아래 링크들을 통해 다른 이슈들에 대해서도 확인해 볼 수 있습니다.&#x20;
  * [Release Note](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.0-Release-Notes)
  * [Tracing 관련기능](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#actuator.micrometer-tracing)



### 라이브러리 vs 프레임워크



|       | 라이브러리                      | 프레임워크                |
| ----- | -------------------------- | -------------------- |
| 사용 방식 | 개발자가 필요에 따라 라이브러리 함수 직접 호출 | 프레임워크에 정의된 규칙에 따라 작성 |
| 제어 흐름 | 주로 개발자가 제어                 | 주로 프레임워크가 제어         |
| 코드 구성 | 라이브러리는 일반적으로 독립적으로 사용      | 애플리케이션의 구조를 정의       |
| 예시    | JQuery, Thymeleaf 등        | Spring, Django 등     |
