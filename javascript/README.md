# Tip-js

## 인터프리트? 컴파일? jit컴파일
- 인터프리트 : 코드를 실행할때 한줄 한줄 코드를 기계어로 변환한후 실행하는 것(자바스크립트는 과거 인터프리터 언어였었다.)
- 컴파일 : 코드를 개발한후 전체를 한번에 기계어로 변환하는 것
- jit컴파일(Just in Time Compile) : 두가지 장점을 모두 합친 모던 브라우저에서 사용하는 방식, 프로그램 실행 시점에 필요한 부분을 즉석에서 컴파일(현재 모던브라우저의 자바스크립트는 jit컴파일 언어이다.)

##### jit컴파일러
- 구글의 V8: 크롬, 오페라, Nodejs
- 모질라의 TraceMonkey: 파이어폭스 3.5+
- MS의 Chakra: IE9+
- Webkit엔진의 JavascriptCore: 사파리


## 전역변수와 지역변수
- 지역변수는 변수의 스코프에 의존적이다.
- 여타 프로그래밍에서 스코프는 블록{} 스코프 이지만 자바스크립트에서는 함수단위 스코프 이다.
- 스코프의 접근 가능한 스코프는 자기 자신을 포함한 상위 스코프이다.
- 전역변수는 메모리의 Data영역에 적재되어 프로그램 종료시까지 유지된다.
- 지역변수는 메모리의 Stack영역에 적재되어 지연변수의 LifeCycle은 함수 호출시에 생성되고 종료시에 시스템에 의해 반환된다.

## 클로져
> 이미 생명 주기가 끝난 외부 함수의 변수를 참조하는 함수

함수를 호출할 때 이전에 호출했던 값을 유지하고 싶어질 경우, 함수가 종료되도 메모리에서 지역변수가 해제되지 않도록 하기 위해 클로져를 사용한다.

##### 클로저 조건
1. 참조하는 대상이 참조하는 녀석과 접근 가능한 스코프에 있어야 한다.
2. 참조하는 대상이 존재하는 함수를 호출하는 게 아니라, 참조하는 녀석이 존재하는 함수를 직접 호출해야한다.
3. 참조하는 대상이 전역 스코프가 아니어야한다.

조건1 : 
참조하는 녀석과 참조대상이 같은 스코프에 있지 않다.<br />
로컬 스코프 c의 접근 가능한 스코프는 자신을 포함한 상위 스코프이다. (로컬스코프 c, 전역스코프)
```js
const a = () => {
    var b = 1;    
}
const c = () => {
    console.log(b);
}
c();    // Uncaught ReferenceError: b is not defined
```

조건2: 참조하는 녀석이 존재하는 함수를 호출하는게 아닌 참조하는 대상이 존재하는 함수를 연속으로 호출해서
호출시마다 참조대상이 초기화 되고 있다.
```js
const a = () => {
    let b = 1;
    const c = () => {
       console.log(b++);
    }
    c();
}
a();    // 1
a();    // 1
a();    // 1
a();    // 1
```

조건2를 만족하기위해 글로벌 스코프에서 참조하는녀석이 존재하는 함수c를 호출해야한다.<br/>
하지만 글로벌 스코프에 c가 존재하지 않기때문에 c를 호출하기 위해선 일급함수 특징을 살려 a함수 에서 c를 리턴해줌으로써 해결해준다.<br/>
함수a를 호출한 결과를 따로 변수에 담아서 호출하지 않고 바로 a함수 호출로 c에 접근하기위해 즉시 실행함수로 a를 즉시 실행해주어서 c를 매번 실행할수있도록 했다.
```js
const a = (() => {
    let b = 1;
    return () => {
        console.log(b++);
    }
})();
a();    // 1
a();    // 2
a();    // 3
a();    // 4
```

##### 클로저의 장점
- 호출할 때마다 기존에 생성했던 값을 유지할 수 있다.
- 외부에 해당 변수(참조하고 있는 변수)를 노출시키지 않는다.

##### 클로저의 단점
- 클로저로 참조하는 변수는 프로그램 종료 시까지 계속 메모리에 할당되있다.

### CustomEvent
```js
// add an appropriate event listener
obj.addEventListener("cat", function(e) { process(e.detail) });

// create and dispatch the event
var event = new CustomEvent("cat", {
  detail: {
    hazcheeseburger: true
  }
});
obj.dispatchEvent(event);
```

### Array.from
> 배열 형식의 개체나 반복 가능한 개체에서 배열을 반환합니다.

```js
// DOM 요소 노드 컬렉션에서 배열을 반환
var elemArr = Array.from(document.querySelectorAll('*'));
var elem = elemArr[0]; // elem contains a reference to the first DOM element
```

```js
// 문자를 배열로 반환
var charArr = Array.from("abc");
// charArr[0] == "a";
```

```js
// 매핑 함수를 사용하여 요소의 값을 변경하며 최종적으로 배열을 반환
var arr = Array.from([1, 2, 3], x => x * 10);
// arr[0] == 10;
// arr[1] == 20;
// arr[2] == 30;
```

### data 어트리뷰트 js 에서 로드 
```html
<div data-keyword="test" click="click"></div>
```

```js
click(event) {
    const keyword = event.currentTarget.dataset.keyword // 로 접근 가능
}
```

### QuerySelect
```js
Array.from(this.el.querySelectorAll('button.btn-remove')).forEach(btn=>{
    btn.addEventListener('click', e=>{
        e.stopPropagation();
        this.onRemove(btn.parentElement.dataset.keyword);       // parentElement 로 부모탐색가능
    })
})
```

### definedProperty

```js
const h1 = document.createElement('h1');
document.body.appendChild(h1);

const viewModel = {};
let model = '';

Object.defineProperty(viewModel, 'model', {
	get() {return model;},
	set(value) {
		model = value;
		h1.innerHTML = model;
	}
});

viewModel.model= 'Hello World'
// 모델이 바뀔경우 view 자동 반영
```