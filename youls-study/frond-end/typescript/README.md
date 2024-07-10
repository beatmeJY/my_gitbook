# TypeScript

## 타입스크립트 기본타입

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



## Type 사용법

```tsx
// restaurant.ts
export type Restaurant = {
    name: string;
    category: string;
    address: Address;
    menu: Menu[];
}

export type Address {
    city: string;
    detail: string;
    zipcode: number;
}

export type Menu {
    name: string;
    price: number;
    category: string;
}
```



## Interface 사용법

```tsx
// Store.tsx
import { Address, Restaurant } from './model/restaurant'

interface Ownprops {
    info:Restaurant,
    changeAddress(address:Address):void
}

// restaurant.ts
export type Restaurant = {
    name: string;
    category: string;
    address: Address;
    menu: Menu[];
}

export type Address {
    city: string;
    detail: string;
    zipcode: number;
}
```



## extends 사용법

### Interface에서 사용법

<pre class="language-tsx"><code class="lang-tsx"><strong>// BestMenu.tsx
</strong>import { Menu } from './model/restaurant'

interface Ownprops extends Menu {
    showBestMenuName(name:string):string
}

// restaurant.ts
export type Menu {
    name: string;
    price: number;
    category: string;
}
</code></pre>

### Type에서 사용법

```tsx
// BestMenu.tsx
import { Menu } from './model/restaurant'

type OwnProps = Menu & {
    showBestMenuName(name:string):string
}

// restaurant.ts
export type Menu {
    name: string;
    price: number;
    category: string;
}
```

### Omit 사용법 (특정 항목만 제거하고 싶을 때)

#### type

```tsx
// restaurant.ts
export type Address {
    city: string;
    detail: string;
    zipcode: number;
}

export type AddressWithoutZip = Omit<Address, 'zipCode'>
```

#### interface

```tsx
// BestMenu.tsx
import { Menu } from './model/restaurant'

interface OwnProps extends Omit<Menu, 'price'> {
    showBestMenuName(name:string):string
}

// restaurant.ts
export type Menu {
    name: string;
    price: number;
    category: string;
}
```

* 두 예제 모두 Omit으로 Menu에서 price를 제외한다. (예제에선 name, catefory만 사용 됨)

### Pick 사용법 (특정 항목만 사용하고 싶을 때)

#### type

```tsx
// restaurant.ts
export type Restaurant = {
    name: string;
    category: string;
    address: Address;
    menu: Menu[];
}

export type AddressWithoutZip = Pick<Restaurant, 'category'>
```

#### interface

```tsx
// BestMenu.tsx
import { Restaurant } from './model/restaurant'

interface OwnProps extends Pick<Restaurant, 'category'> {
    showBestMenuName(name:string):string
}

// restaurant.ts
export type Restaurant = {
    name: string;
    category: string;
    address: Address;
    menu: Menu[];
}
```

* 두 예제 모두 Pick으로 Restaurant에서 category만 사용한다.



## React

### 리액트에서 타입스크립트 컴포넌트를 작성하는 방법

#### React Function Component

```tsx
// App.tsx
function App(): JSX.Element {
    return (
        <div>Hi React</div>
    )
}
```

* function 컴포넌트의 반환타입인 JSX.Element 를 작성하여 주었고 그로 인해 확장자도 .tsx를 사용한다.
  * 물론 `타입 추론`이라는 개념 때문에 여기선 반환 타입을 작성해주지 않아도 추론하여 줄 수 있다.
* **JSX 문법**: Javascript XML로 자바스크립트 안에 HTML이나 CSS 등 UI를 표기하기 위한 문법을 넣을 수 있다.

### 리액트에서 UI 이벤트를 제어하는 방법

```tsx
import { MouseEvent } from 'react';

function App() {
    const showAlert = (event: MouseEvent<HTMLButtonElement>) => {
        console.log(event);
    }
    
    return(
        <button onClick={showAlert}>show</button>
    )
}
```

```jsx
import { ChangeEvent, useState } from 'react';

function App() {
    const [todo, setTodo] = useState('');
    function updateTodo(event: ChangeEvent<HTMLInputElement>){
        setTodo(event.target.value);
    }
    
    return(
        <input type="text" value={todo} onChange={updateTodo}/>
    )
}
```
