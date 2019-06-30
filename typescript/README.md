# Tip-typescript

## tsc 명령어

### -p 옵션 : 루트 디렉토리 설정
```bash
tsc -p ./       현재 경로를 루트 디렉토리로로 설정
tsc -p ../      상위 경로를 루트 디렉토리로 설정
```
### -t 옵션 (target) : 컴파일할 ECMA 스크립트 버전을 명시
```bash
tsc hello.ts -t ES5
```
> ES3(기본값), ES5, ES6(ES2015), ES2016, ES2017, ESNext

### 여러파일 컴파일하기
```bash
tsc file1.ts file2.ts file3.ts
tsc @ts-files.txt
tsc file1.ts, file2.ts --out file3.js
```

### tsconfig
```json
// 옵션
compieOptions: {
    target: 'es5',                  // 컴파일된 후 변환될 ecma스크립트 버전
    module: 'commonjs'              // 모듈형식
    sourceMap: true                 // 컴파일 후 맵파일의 생성 여부 지정(브라우저 환경에서 타입스크립트 디버깅을 위해 존재하는것이 맵 파일)
    strict: true                    // 엄격 타입 검사 모드 활성화 여부
    noImplicitAny : false (default) // true일경우 any타입 지정 강제, 아무타입도 지정 안 할 경우 에러
    esModuleInterop                 // true면 commonJS모듈을 디폴트 모듈처럼 호출할 수 있다
    outFile: './dist/out.js'        // 최종 컴파일될 단일의 자바스크립트 파일
    or
    outDir: './dist'                // 최종 컴파일될 자바스크립트 파일들이 들어갈 위치
},
extends: './config/base.json'       // 상위 설정 파일 지정
```

## 연산자
```js
// 산술 연산자
console.log(1 + false);         // 숫자와 불리언은 더하기 연산할 수 없음
console.log(false + false);     // 불리언끼리는 더하기 연산할 수 없음
console.log(10 / '2')           // 문자열 타입의 숫자는 연산 불가능

// 비교 연산자
if(1 == true)                   // 타입이 다른 피연산자 간에는 비교연산이 불가능함
```

배열을 인자로 받는 함수에서의 디스트럭쳐링과 타입선언
```js
function f([first, second]:[number, string]) { ... }
```

## 함수
```js
function max(x: number, y:number): number {}         // 타입 선언
function max(x: number, y:number = 2): number {}         // 타입 선언과 초기화
function concat(...restParameter: string[]) {}          // rest 파라미터의 타입선언

function sum(a: number, b?: number =2) {}               // 선택매개변수와 초기화는 동시에 사용 불가

function sum2(a: number, b?: number) { 
    if(b === undefined) {                               // 선택매개변수 사용시에는 초기화코드를 별도로 넣어 주어야 한다.
        b = 0;
    }
    return a+b;
}
console.log(sum2(1));
```

interface 활용
```js
interface PersonType {
    name: string;
    hello(this: PersonType, name2: string): string;
}

let typePerson: PersonType = {
    name: 'happy',
    hello: function(this: PersonType, name2:string) string {
        return `hello ${this.name}`;
    }
}
```


익명함수 타입 선언
```js
let myConcat: (str1: string, str2: string): string => {return str1 + str2;}                     // 함수 선언과 동시에 타입선언
let myConcat: (str1: string, str2: string) => string = (str, str2) => {return str1 + str2;}     // 함수선언과 타입선언을 분리

// 함수의 타입을 변수화 하여 할당
type calcType = (str1: string, str2: string) => string;
let addCalc: calcType = (a, b) => a + b;
```

콜백함수 타입선언
```js
type callbackType = (meesage: string) => void;
let callback: callbackType = message => message;

testFn('hello', callback);
```

## 클래스
- 상속관계
- 포함관계
  - 합성(composition) 관계
  - 집합(aggregation) 관계

상속관계
```js
class Bus extends Car {
    constructor() {
        super();
    }
}
```
> 버스는 자동차이다 IS-A 관계라고도 한다.

포함관계 - 합성관계
```js
class Car {
    private engine;
    constructor() {
        this.engine = new Engine();
    }
}
```
> - 자동차는 엔진을 가지고 있다. HAS-A 관계라고도 한다.
> - Car 클래스 내부에서 Engine 클래스를 생성기 때문에 Car 인스턴스를 null로 제거 시키면 engine 인스턴스도 같이 제거된다. 강한 관계

포함관계 - 집합관계
```js
class Car {
    private engine;
    constructor(engine: Engine) {
        this.engine = engine;
    }
}
```
> - 자동차는 엔진을 가지고 있다. HAS-A 관계라고도 한다.
> - Car 클래스 외부에서 Engine 클래스의 인스턴스를 받기 때문에 Car 인스턴스를 null로 제거해도 외부의 engine 인스턴스는 제거되지 않는다. 약한 관계

## 인터페이스
```js
// 인터페이스 선언
interface Dog {
    run(): void;
    getStatus(): { runningSpped: number; };
}

interface Bird {
    fly(): void;
    getStatus(): { flightSpeed: number; };
}

interface DogBird extends Dog, Bird {
    getStatus() : {
        runingSpped: number, flightSpeed: number 
    }
}

// 인터페이스 사용
class newAnimal implements DogBird {
    run(): void {}
    fly(): void {}
    getStatus(): {runningSpeed: number, flightSpeed: number;} {
        return { runningSpeed: 10, flightSpeed: 20 };
    }
}
```
> 다중상속을 한 부모에 같은 메서드가 있는데 반환타입이 다를경우 두 반환타입을 전부 선언해주어야한다.

배열 내 객체 타입 지정
```js
// 클래스를 이용해 배열 타입을 지정
class Person3 {
    public full: string;
    constructor(public name: string, public city: string) {
        this.full = name + '(' + city + ')';
    }
}
let person3: Person3[] = [
    new Person3('kim', 'seoul'),
    new Person3('lee', 'busan'),
]

// 인터페이스를 이용해 배열 타입 지정
interface Person {
    name: string;
    city: string;
}
let person4: Person[] = [
    {name: 'kim', city: 'seoul'},
    {name: 'lee', city: 'busan'}
]
```

오버라이딩 메서드
- 오버라이든 클래스/메서드 : 오버라이딩할 메서드가 있는 부모클래스와 그에 속해 있는 메서드
- 오버라이딩 메서드 : 부모 클래스에 정의된 메서드를 가지고 자식 클래스에서 새로 구현한 메서드

> - 조건1: 오버라이든 메서드의 매개변수 타입은 오버라이딩 메서드와 같거나 상위 타입이어야 한다.(오버라이딩 메서드의 매개변수 타입이 Any타입이면 제외)
> - 조건2: 오버라이든 메서드의 매개변수 개수가 오버라이딩 메서드와 같거나 많아야 한다.(단 조건1이 성립된다는 전제가 있어야 함)

## 모듈

모듈 형식 변환 명령어
```js
tsc -t es3 -m <모듈형식>
tsc -t es5 -m <모듈형식>
tsc --module <모듈형식> <변환 파일명>
```
모듈 형식 종류
- none
- commonjs
- amd
- system
- umd
- es6 or es2015

## 타입
### 유니언 타입
```js
// 타입 선언
var x: string | number;
x = 'happy';
x = 1234;

// 타입선언과 동시에 값 할당
var x: string | number = 1;
var y: boolean | string = 'hello';
```

### 문자열 리터럴 타입
```js
let event: "keyup" = "keyup";   // 가능
let event: "keyup" = "keyup2";  // 불가능

type EventType: 'keyup' | 'mouseover';
const myEvent: EventTyp = 'keyup';
```

### 룩업 타입
```js
interface Profile {
    name: string;
    gender: string;
    age: number;
}
type Profile1 = keyof Profile;
let pValue: Profile1 = 'name';
```

### non-nullable 타입
```js
{
    'compilerOptions': {
        'strictNullChecks': true
    }
}
let studentName: string;
studentName = null;     // 오류

//할당방법 : 유니언 타입으로 할당가능
let title: string | null;
let title2: string | undefined;
title = null;
title2 = undefined;
```

### 네버타입
1. 함수에 닿을 수 없는 코드 영역이 있어 반환값이 존재하지 않을 때
2. 함수에 Throw 객체가 반환돼 오류가 발생할 때

```js
const neverTouch = (): never => {
    while(true) {
        console.log('never');
    }
    // console.log();       <- 닿을 수 없는 코드
}
let resultNever: never = neverTouch();

function error(message: string): never {
    throw new Error(message);
}
```

### this타입
```js
interface ListItem {
    getHead(): this;
    getTail(): this;
}
```