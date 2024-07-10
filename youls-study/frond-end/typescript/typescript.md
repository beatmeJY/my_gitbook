# TypeScript 타입 종류

## 기본 타입

```tsx
const a: string = 'hi';
const num: number = 10;
const isLogin: boolean = true;
const arr: number[] = [1,2,3];
const product: object = {name: 'test'};
const result: any = 10 + {} [];
```

* string: 문자열을 의미한다.
* number : 숫자를 의미한다.
* boolean: true, false의 값을 가질 수 있다는 것을 의미한다.
* number\[]: \[]는 배열을 의미하고, 여기선 number이므로 숫자 만을 포함하는 배열을 의미한다.
* object: 객체를 의미한다.
* any: 주로 어떤 타입이 올지 모를 때 사용한다. (사용은지양하는 것이 좋다.)



## 제네릭 타입

```tsx
interface Person {
    name: string;
    age: number;
}

interface Developer {
    name: string;
    age: string;
}

interface Anyone<T> {
    name: string;
    age: T;
}

const test: Anyone<number> = {
    name: 'josh';
    age: 100;
}
```

* 예를 들어 age를 숫자로 입력하는 경우와 string으로 입력 받는 경우가 있다면
* 위와 같이 Anyone 제네릭 인터페이스를 만들어 해결 할 수 있다.
