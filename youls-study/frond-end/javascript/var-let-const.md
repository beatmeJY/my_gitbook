# var, let, const 차이

## 변수 선언 방식

### var

* window 객체의 속성으로 추가되어 글로벌 객체이다.
* 따라서전역 스코프에서 접근이 가능하다.
* 재할당과 재선언이 가능하다.

### let

* 블록 스코프를 가진다.
* 재할당이 가능하다.
* 재선언은 불가능하다.

### const

* 블록 스코프를 가진다.
* 재할당과 재선언이 불가능하다.



## 호이스팅

> 변수는 `선언 단계` > `초기화 단계` > `할당 단계` 에 걸쳐 생성된다.

#### var

```js
// 스코프의 선두에서 선언 단계와 초기화 단계 한번에 이루어진다.
// 따라서 변수 선언문 이전에 변수를 참조할 수 있다.
console.log(test); // undefined

var test;
console.log(test); // undefined

test= 1; // 할당문에서 할당 단계가 실행된다.
console.log(test); // 1
```

* var는 선언 단계와 동시에 `undefined`로 초기화 단계 한번에 이루어진다.
* 따라서 변수 선언문 이전에 변수를 참조할 수 있다

#### let, const

```js
// 스코프의 선두에서 선언 단계가 실행된다.
// 아직 변수가 초기화(메모리 공간 확보와 undefined로 초기화)되지 않았다.
// 따라서 변수 선언문 이전에 변수를 참조할 수 없어 참조에러가 발생한다.
console.log(test); // ReferenceError: foo is not defined

let test; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(test); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(test); // 1
```

* let과 const는 호이스팅 되지만, 초기화는 되지 않는다.
* 따라서 선언 전에 접근하려고 하면 참조 에러가 발생한다.
* 따라서 let과 const는 일시적 사각지대를 가진다.
