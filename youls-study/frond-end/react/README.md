# React

### export

```tsx
class Content extends Component{
    render(){
        return (...)
    }
}

export default Content
```

* 해당 파일을 사용하는 외부에서 어떤 것을 사용할 수 있게 해줄 건지 명시할 수 있다.

### Debugger

```jsx
<a href="/" onClick={function(e){ 
    debugger;    
}}></a>
```

* 위와 같이 debugger 를 작성하고 실행 시킬 경우 웹 브라우저에서 개발자 도구 Sources 탭에서 편리하게 해당 지점에서 멈추고 디버깅을 할 수 있다.



### 여러 동작 과정 설명

* 리액트에서는 `props`나 `state`의 값이 바뀌면 해당 컴포넌트의 `render()`가 재 호출 된다.
