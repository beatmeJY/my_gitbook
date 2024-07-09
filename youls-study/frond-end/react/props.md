---
description: 리액트 속성
---

# Props

### Props 사용방법

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
    render() {
        return (
            <div className="App">
                <Content title="React" sub="Hello!! Let’s study together!!"></Content>
            </div>
        )
    }
}
```
