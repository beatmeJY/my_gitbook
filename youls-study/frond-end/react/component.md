# Component

### 컴포넌트 생성 방법

```tsx
import React, { Component } from 'react';

// Subject.js
class Subject extends Component {
    render() {
        return (
            <div>Hi React</div>
        )
    }
}

// Content.js
class Content extends Component {
    render() {
        return (
            <h1>React</h1>
            <div>Hello!! Let’s study together!!</div>
        )
    }
}

// App.js
class App extends Component {
    render() {
        return (
            <div className="App">
                <Subject></Subject>
                <Content ></Content>
            </div>
        )
    }
}
```



### 📚 참고자료

* [컴포넌트 효율적으로 나누기](https://www.inflearn.com/course/lecture?courseSlug=react-%EC%83%9D%ED%99%9C%EC%BD%94%EB%94%A9\&unitId=20319\&tab=curriculum)
