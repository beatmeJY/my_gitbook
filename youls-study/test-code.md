# 🐞 Test Code

## 테스트

### 목 객체를 사용하는 이유가 무엇일까요?

* 주로 단위 테스트를 하기 위해서 사용합니다.
* 테스트 시에 주로 다른 특정 클래스나 모듈이 다른 클래스나 모듈에 의존하게 됩니다.
* 이러한 의존성을 해결하기 위해서 목 객체를 사용하여 코드의 의존성을 분리하여 격리된 테스트 환경을 구축 할 수 있습니다.
* 특정 메소드 호출이나 반환 값을 지정하여 예외를 발생 시킬 수 도 있어 원하는 상황을 재현하고 특정 코드 경로를 테스트 할 수 도 있습니다.
* 외부 서비스와의 테스트 시에도 호출 하지 않고 테스트를 할 수 있습니다.
