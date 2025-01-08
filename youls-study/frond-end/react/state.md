# State

## Props와 State 차이

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

#### Props

* props는 read-only이다.
* props는수정할 수 없다.

#### State

* state는 비동기적으로 변경이 가능하다.
* state는  `this.setState`를 통해서 수정할 수 있다.



## State 작성 방법

```tsx
// Content.js
class Content extends Component {
    render() {
        return (
            <h1>{this.props.title}</h1>
            <div>{this.props.sub}</div>
        )
    }
}

// App.js
class App extends Component {
    constructor(props){
        super(props);
        this.state = {
            subject:{
                title:'React',
                sub: 'Hello!! Let’s study together!!' 
            }
        }
    }
    render() {
        return (
            <div className="App">
                <Content 
                    title={this.state.subject.tile} 
                    sub={this.state.subject.sub}>
                </Content>
            </div>
        )
    }
}
```

