# Event

## e.target

```jsx
<a href="/" data-id="hi" onClick={function(e){ 
    e.target.dataset.id;
}}></a>
```

* target는 해당 태그를 가르킨다.
* 위 코드에서는 `a`태그의 `id`정보를 가져올 수 있다.



## 이벤트 처리 제어 메서드

### e.preventDefault()

```jsx
<a href="/" onClick={function(e){ 
    e.preventDefault();
}}></a>
```

* 이벤트의 기본 동작을 막는다.

### e.stopPropagation()

```jsx
<a href="/" onClick={function(e){ 
    e.stopPropagation();
}}></a>
```

* 이벤트가 더 이상 상위 요소로 전파되는 것을 막는다.



## 상위 컴포넌트로 이벤트 사용 예제

```jsx
import React, { Component } from 'react';
import TOC from "./components/TOC";

// App.js
class App extends Component {
    constructor(props){
        super(props);
        this.state = {
            mode: '',
            contents: [
                {id:1, title:'HTML', desc: 'HTML is for information'},
                {id:2, title:'CSS', desc: 'CSS is for design'},
                {id:3, title:'JavaScript', desc: 'javaScript is for interactive'}
            ]
        }
    }
    render() {
        var _title, _desc = null;
        var i = 0;
        while(i < this.state.contents.length){
            var data = this.state.contents[i];
            if(data.id === this.state.selected_content_id){
                _title = data.title;
                _desc = data.desc;
            }
        }
        return (
            <div className="App">
                <TOC
                onChangePage={function(id){
                    this.setState({
                        mode: 'read',
                        selected_content_id:Number(id)
                    });
                }.bind(this)}
                data={this.state.contents}
                ></TOC>
            </div>
        )
    }
}

// TOC.js
import React, { Component } from 'react';

class TOC extends Component {
    render(){
        var lists = [];
        var data = this.props.data;
        var i = 0;
        while(i < data.length){
            list.push(
                <li key={data[i].id}>
                <a
                    href={"/content"+data[i].id}
                    data-id={data[i].id}
                    onClick={function(id, e){
                        e.preventDefault();
                        this.props.onChangePage(id);
                    }.bind(this, data[i].id)}
                >{data[i].title}</a>
                </li>
            );
            i = i + 1;
        }
        return (
            <nav>
                <ul>
                    {lists}
                </ul>
            </nav>
        )
    }
}
```

