### 메서드 생성시 constructor에서 this 바인드하기
```
this.handler = this.handler.bind(this);
```

### setState 문법
```
this.setState({
	selectedKey: key
})
```

### react-addons-update 사용하여 객체or배열 state 업데이트
```
this.setState({
	contact: update(this.state.contact, {$push: [contact]})
})

this.setState({
	contact: update(this.state.contact, {$splice:[[this.state.selectedKey, 1]]})
})

this.setState({
	contact: update(this.state.contact, {
                [this.state.selectedKey]: {
                    name: {$set: name},
                    phone: {$set: phone}
		}
	})
})
```

### props 으로 넘긴 함수를 자식컴포넌트에서 바로 사용 (인자있음)
```
[propName]={()=>{this.handleClick(i)}}	// 부모에서넘김
onClick={this.props.[propName]}		// 바로 사용
```

### props 으로 넘긴 함수를 자식컴포넌트에서 바로 사용 (인자없음)
```
[propName]={this.handleClick}	// 부모에서넘김
onClick={this.props.[propName]}		// 바로 사용
```

### ref생성 및 사용
```
<input type="text" ref={(ref)=>{this.nameInput=ref}} />     // 지정
this.nameInput.focus();     // 사용
```
> render() 메소드 내부나 contstructor() 내부에서는 ref 를 사용하지 못한다.

### es6 spread 문법으로 객체 내 특정 값만 변환하기
```
var object = {
  a: 1,
  b: 2,
  innerObject : {
    c: 3,
    d: 4
  }
}
var result = {
  ...object,
  b:3,
  innerObject: {
    ...object.innerObject,
    d: 5
  }
};
console.log(result);   // {a:1, b:3, innerObject: {c:3, d:5}}
```

### react redux - api call process - state set
```js
const initial = {
    loading: false,
    done: false,
    error: null
} 
const start = {
    loading: true,
    done: false,
    error: null
}
const finish = {
    loading: false,
    done: true,
    error: null
}
const exeption = {
    loading: false,
    done: false,
}
```




