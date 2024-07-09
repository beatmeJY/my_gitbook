# State

### State 작성 방법

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

