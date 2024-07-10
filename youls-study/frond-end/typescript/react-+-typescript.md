# React + Typescript

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
import { MouseEvent, ChangeEvent, useState } from 'react';

function ButtonHandler() {
    const showAlert = (event: MouseEvent<HTMLButtonElement>) => {
        console.log(event);
    }
    
    return(
        <button onClick={showAlert}>show</button>
    )
}

function InputHandler() {
    const [todo, setTodo] = useState('');
    
    function updateTodo(event: ChangeEvent<HTMLInputElement>){
        setTodo(event.target.value);
    }
    
    return(
        <input type="text" value={todo} onChange={updateTodo}/>;
    )
}

export { ButtonHandler, InputHandler };
```
