# Spring Container

## 스프링 컨테이너 란?

***

* 추후 보강 예정

### 스프링의Bean 등록 과정

* 추후 보강 예정

### 왜 스프링은 Bean을 프록시 객체로 감싸서 등록할까?

* 추후 보강 예정



## DI (Dependency Injection)

***

### Spring의 DI란?

<figure><img src="../../.gitbook/assets/image (104).png" alt="" width="563"><figcaption><p><a href="https://velog.io/@damiano1027/Spring-%EC%9D%98%EC%A1%B4%EC%84%B1-%EC%A3%BC%EC%9E%85-%EC%A0%9C%EC%96%B4%EC%9D%98-%EC%97%AD%EC%A0%84">https://velog.io/@damiano1027/Spring-%EC%9D%98%EC%A1%B4%EC%84%B1-%EC%A3%BC%EC%9E%85-%EC%A0%9C%EC%96%B4%EC%9D%98-%EC%97%AD%EC%A0%84</a></p></figcaption></figure>

* 의존성 주입(Dependency Injection) 이라고 부르며 객체가 실제로 필요한 의존성을 직접 생성하는 것이 아니라 외부에서 주입받도록 하는 것을 의미합니다.



### DI의 장점은?

* 컴포넌트 간의 강합 결합을 피할 수 있어 유연성과 유지보수에 효과적입니다.
* 이는 구현 자체를 수정하지 않고도 새로운 구현체로 교체하거나 주입 받는 대상을 변경 할 수 있습니다.
* 테스트 시에도 가짜 Mock 객체 등을 활용하여 테스트하기 유연한 코드를 만들 수 있습니다.
* 생성자 등 파라미터에 의존성이 명시적으로 드러나 가독성을 향상 시킬 수 있습니다.



### DI의 단점은?

* 실행(런타임) 시 주입하여 컴파일 시에 오류가 발생하지 않아 디버깅에 어려움이 생길 수 있습니다.
* 큰 프로젝트에서는 컴포넌트 간의 관계를 정의하고 설정하기에 해야 하는 작업이 늘어납니다.
* 의존성이 전이되는 등 코드의 가시성이 감소하고 코드를 이해하기 어렵게 만들 수 있어,&#x20;
* 이미 진행 중인 프로젝트에 새로운 개발자가 투입된다면 학습 곡선이 존재 할 수 있습니다.



### DI의 3가지 방법

#### 생성자 주입 방법

```java
public class Car { 
    private Engine engine;
    public Car(Engine engine) {
        this.engine = engine;
    }
}
```

* 의존성을 객체의 생성자를 통해 주입하는 방법입니다.
* 객체를 생성할 때 필요한 의존성을 외부에서 전달 받도록 하는 것입니다.

#### 수정자(Setter) 주입  방법

```java
public class Car {
    private Engine engine;
    public void setEngine(Engine engine) {
        this.engine = engine;
    }
}
```

* setter 메서드와 같은 수정자를 통해 주입하는 방법입니다.
* 주로 의존관계가 변경 가능성이 있는 상황에서 사용됩니다. (final 키워드 사용 불가)

#### 필드주입 방법

```java
public class car { 
    @Autowired
    private Engine engine;
}
```

* 프레임워크나 라이브러리를 통해 주입하는 방법입니다.
* 테스트 하기에 어려움이 있어 권장되지 않는 방법입니다.
* 실제 코드와는 무관한 테스트코드 등의 간단한 로직에서만 사용하는 것이 좋습니다.



### Autowired vs Resources vs Inject

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



### @Qualifier란

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

## IOC 란?

<figure><img src="../../.gitbook/assets/image (105).png" alt=""><figcaption><p><a href="https://www.codingknownsense.com/inversion-of-control-and-dependency-injection/">https://www.codingknownsense.com/inversion-of-control-and-dependency-injection/</a></p></figcaption></figure>

* 제어의 역전(Inversion Of Control) 이라고 불리며 스프링에서도 채택되어 사용되고 있는 소프트웨어 디자인패턴 중 하나입니다.
* 주로 의존성 주입(DI)와 함께 사용되며 객체의 생성, 생명 주기, 의존성 관리 등을 개발자가 아닌 외부에 위임하는 것을 목적으로 합니다.



###
