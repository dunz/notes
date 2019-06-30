# ES6 Tip

### 1. 스프레드 문법

```js
let obj = {a:1, b:2, c:{d:3, e:5, f:6}};
let obj2 = {c:{d:4}};
console.log({...obj, ...obj2});
//  Object = {
//    a: 1,
//    b: 2,
//    c: {
//        d:4     // c를 덮어씌우며 제할당 했기 때문에 e와 f가 사라진다
//    }
//  }

// 해결법
console.log({...obj, c:{...obj.c, d:4}});   // ...obj.c 로 obj c안의 프로퍼티들을 먼저 가져온다
```

### 2. str 함수

```js
const str = 'hello world';
str.startsWidth('hello')    // true
str.endsWidth('world')      // true
str.includes('aaa');        // false
```

### for of

```js
const str = '헬로 월드';
for(const value of str){
	console.log(value);
}
// 헬
// 로
// 
// 월
// 드
const list = [1,2,3]
for(const value of list){
	console.log(value);
}
// 1
// 2
// 3
iterable 객체가 아닐 경우 에러 발생
```

> for in의 단점은 prototype 으로 추가된 상위 객체의 요소까지도 출력된다.

```js
const list = [1,2,3];
Array.prototype.aa = function (){};
for (const key in list){
	console.log(list[key]);
}
// 1
// 2
// 3
// f () {}      // 의도치 않은 요소까지 출력
```

### from
```js
function addMark() {
    let newArray = Array.from(arguments);
    let newData = newArray.map(value=>value+'!');  
    console.log(newData);   // ['1!', '2!', '3!']   ;;
}
addMark(1,2,3);
```
> arguments 는 배열처럼 생겼지만 배열이 아니기 때문에 map 을 쓸수 없다. 그래서
Array.from() 메서드를 사용해서 인자로 arguments 를 넘겨 arguments를 배열로 받아올수 있다.

### typecheck시 유용한 방법
```js
const li = document.querySelector('li');
const filteredArray = Array.from(li).filter(item=>item.innerText.includes('e'));
console.log(toString.call(li));     // typecheck 시 유용한 방법
```

### Destructuring Array
```js
const list = ['samsung', 'apple', 'melon'];
let [aaa,,ccc] = list;
console.log(aaa, ccc); // samsung, melon
```

### Destructuring Object
```js
const obj = {
    name: 'crong',
    address: 'korea',
    age: 10
}
const {name, age} = obj;
console.log(name, age);         // property와 동일한 변수명으로 할당

const {name:myName, age:myAge} = obj;
console.log(myName, myAge);      //새로운 변수명으로 할당
```

### Destructuring Array & Object Sample
```js
const news = [
    {
        title: 'sbs',
        img: 'image1'
    },
    {
        title: 'mbc',
        img: 'image2'
    }
];
const [,mbc] = news;
const {title, img} = mbc;
console.log(title, img);

const [, {title, img}] = news;
console.log(title, img);
```

### Set
> Array를 개선한 자료구조

```js
const mySet = new Set();
console.log(toString.call(mySet));  // [object Set]

mySet.add('aaa');
mySet.add('bbb');
mySet.add('aaa');

mySet.forEach(v=>console.log(v));   // aaa, bbb
console.log(mySet.has('aaa'))   // true
mySet.delete('aaa');
```
> Set : 중복없이 유일한 값을 저장하려고 할 때, 이미 존재하는지 체크할 때 유용.

### WeakSet
> WeakSet : 참조를 가지고 있는 객체만 저장이 가능하다.
> 객체형태를 중복없이 저장하려고 할 대 유용하다.

```js
const arr = [1,2,3,4];
const arr2 = [5,6,7,8];
const obj = {arr, arr2};
const ws = new WeakSet();
ws.add(arr);
//ws.add(111);        //Invald value used in weak set
//ws.add('111');
ws.add(function(){}); 
ws.add(arr2);
ws.add(obj);

arr = null;

console.log(ws.has(arr), ws.has(arr2));     //false true
// 참조객체가 사라지면 가비지컬렉션에 의해 제거된다.
```

### Map & WeakMap
> Object를 개선한 자료구조
> map은 key/value 구조

```js
const wm = new WeakMap();
const myFun = function ();
// 이 함수가 얼마나 실행됐는지를 알려고 할 때.
wm.set(myfun, 0);
let count = 0;
for(let i=0; i<10; i++) {
   count = wm.get(myFun);   // get value
   count ++;
   wm.set(myFun, count);
}
console.log(wm.get(myFun)); // 10
myFun = null
console.log(wm.get(myFun)); // undefined
console.log(wm.has(myFun)); // false
```

### WeakMap 활용

```js
// 기존
function Area(height, width) {
    this.height = height;
    this.width = width;
}
Area.prototype.getArea = () => this.height * this.width;
let myArea = new Area(10, 20);
console.log(mySarea.getArea()); // 200
console.log(mySarea.height);    // 10

// WeakMap (Private 변수 만들기)
const wm = new WeakMap();
function Area(height, width) {
    wm.set(this, {height, width});
}
Area.prototype.getArea = () => {
    const {width, height} = wm.get(this);
    return width * height;
}
let myArea = new Area(10, 20);
console.log(mySarea.getArea()); // 200 
console.log(mySarea.height);    // undefined

myArea = null
console.log(wm.get(myArea)) // false // 가비지 컬렉션에 의해 정리됨.
```

### Tagged Template literals
```js
const data = [
    {
        name: 'coffee--bean',
        order: true,
        items: ['americano', 'milk', 'green-tea']
    },
    {
        name: 'starbucks',
        order: false
    }
];

function fn(tags, name, items) {
    if(typeof items === 'undefined') {
        items = '주문가능한 상품이 없습니다.'
    }
    return tags[0] + name + tags[1] + items + tags[2];
}

const template = fn`<div>welcome ${data[1].name} !!</div>
    <h2>주문가능항목</h2><div>${div[1].items}</div>`;

console.log(template);
```

### Default parameter
```js
const sum = ({a=3, b={value:4}}) => {
	return a*b.value;
}
sum({})
```

### Object create
```js
const healthObj = {
    showHealth() {
        console.log('오늘 운동시간' + this.healthTime);
    }
}
const myHealth = Object.create(healthObj);
myHealth.healthTime = '11:20';
myHealth.name = 'dunz';

console.log(myHealth);       // showHelth 가 new 키워드로 생성한 것처럼 prototype안에 들어간다.
// 다만 내장 property를 추후에 추가해줘야 하는 단점이있다. 이것은 Object.assign으로 보완
const myHealth = Object.assign(Object.create(healthObj), {
    name: 'crong',
    lastTime: '11:20'
})
```

### setPrototypeOf
```js
const healthObj = {
    showHealth() {
        console.log('오늘 운동시간: ' + this.healthTime);
    },
    setHelath(newTime) {
        this.healthTime = newTime;
    }
}

const myHealth = {
    name: 'dunz',
    lastTime: '11:20'
};

Object.setPrototypeOf(myHealth, healthObj);
console.log(myHealth);
// myHealth prototype에 heatlhObj 객체의 정보가 추가된다.

const newObj = Object.setPrototypeOf({
    name: 'dunz',
    lastTime: '11:20'
}}, healthObj);

```

### setPrototypeOf Chain
```js
const healthObj = {
    showHealth() {
        console.log('오늘 운동시간: ' + this.healthTime);
    },
    setHelath(newTime) {
        this.healthTime = newTime;
    }
}
const childObj = {
    getAge() {
        return this.age;
    }
}

const childObj = Object.setPrototypeOf({
    age:22
}, childObj)
Object.setPrototypeOf(childObj, heatlhObj);

childObj.setHealthTime("11:55");
childObj.showHealth();

// childObj에 없지만 proto에 추가된 요소를 타고 올라가서 healthObj의 메서드를 실행할수 있게 된다.
// 일종의 상속 구조 구현 가능
```

### Proxy로 interception 기능 구현
```js
const myObj = {name:'dunz'};
const proxy = new Proxy(myObj, {
    get(target, property, receiver) {
        console.log('get value');
        return target[property];
        // or return Reflect.get(target, property);      // 권장
        
        return (property in target) ? target[property] : 'anonymous'; // 없는 property 접근시 default 설정 가능
    },
    set(target, property, value) {
        console.log('set value');
        target[property] = value;
    }
})
proxy.name = 'asdf';    // set value 'asdf'
proxy.name  // get value 'asdf'

const proxy = new Proxy({name:'dunz'}, {})      // myObj 접근제한 캡슐화
```
> target 객체의 변화를 감지해서 추가 작업을 해줄 수 있다.

### Array Destructuring 활용하여 switching하기
```js
const arr = [1,2,3,4];
[arr[0], arr[1]] = [arr[1], arr[0]];
```

### isPrime(소수구하기)
```js
const isPrime = num => {
    for(let i = 2, s = Math.sqrt(num); i <= s; i++) {
        if(num % i === 0) return false; 
    }
    return num !== 1 && num !== 0;
}
```

### Object, Array 새로운 메소드
Object
```js
var obj = {a:1, b:2, c:3};
obj.$add('d', 4);
obj.$set('a':, 2);
obj.$delete('c');
```

Array
```js
var arr = [1, 2, 3, 'd'];
arr.$set(0, 4);
arr.$remove(1);
arr.$remove('d');
```
