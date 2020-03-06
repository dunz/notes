# Jest

## Install

### 설치
```sh
yarn add -D jest    // 설치
```

### 구성
```sh
yarn jest --init    // 초기환경 구성 jest.config.js 생성됨
```
> `jest.config.js` 는 default로 ROOT 경로 필수

### 바벨 사용
설치 후 `babel.config.js`파일을 구성할 경우 jest에서 ES6사용 가능하다.

다만 `jest.config.js`에 `transform` 프로퍼티가 선언되있을경우 `babel-jest`를 별도 선언해주어야한다.
```sh
yarn add -D babel-jest @babel/core @babel/preset-env
```
```js
module.exports = {
  presets: [
    ['@babel/preset-env', {targets: {node: 'current'}}]
  ],
};
```

babel.config.js return type을 함수로 지정할 수 있고

첫번째 인자인 api의 `api.env('test')`(process.env.NODE_ENV === 'test')로 

현재 환경변수가 test로 설정되어있는지 받아와서 환경별 조건처리를 해줄 수 있다.

```js
module.exports = api => {
    const isTest = api.env('test');
    
    return {
        presets: [
            ['@babel/preset-env', {targets: {node: 'current'}}]
        ],
    };
}
```

### typescript 사용
```sh
yarn add --dev @babel/preset-typescript   // 설치
```

`babel.config.js` 바벨 설정 추가
```js
module.exports = api => {
    const isTest = api.env('test');
    
    return {
        presets: [
            ['@babel/preset-env', {targets: {node: 'current'}}],
            '@babel/preset-typescript'
        ]
    };
};
```



## Matchers

### Truthiness
- .toBe(): 동일검사
- .toEqual(): 동일검사 (객체 or 배열)
- .not.toBe(): 부정검사
- toBeNull(): null 검사
- toBeUndefined(): undefined 검사
- toBeDefined: toBeUndefined의 반대 선언됐는지 검사
- toBeTruthy: truthy value인지 검사(true, 1, {}, [], '0')
- toBeFalsy: falsy value인지 검사(false, 0, null, undefined, '')

### Numbers
- .toBeGreaterThan(3): 3보다 큰지 검사
- .toBeGreaterThanOrEqual(3): 3보다 크거나 같은지 검사
- .toBeLessThan(3): 3보다 작은지 검사
- .toBeLessThanOrEqual(3): 3보다 작거나 같은지 검사
- .toBeCloseTo(0.3): 0.3과 같은지 검사, 부동소수점일 경우 , toBe, toEqual이 아닌 toBeCloseTo 사용

### Strings
- .toMatch(/foo/): string에 foo가 포함되어 있는지 검사 (정규식 사용)

### Arrays 
- .toContain('foo'): Array에 'foo'가 포함되어 있는지 검사

### Exceptions
- expect(fn).toThrow('' or //): 함수 실행시 에러를 던지는지 검사, 에러메시지를 넘겨 일치하는지 검사 가능

## Testing Asynchronous Code
일반적으로 코드를 실행하기 전에 테스트가 끝나버린다.

비동기 완료 콜백 실행 후까지 테스트를 미루기 위해 test함수의 인자값으로 done을 추가하여 비동기 콜백에서 테스트 완료 후 done함수를 실행해주어야 한다.
추가로 expect 개수만큼 `expect.assertions(1)`를 명시적으로 지정해주는것이 좋다.

```js
test('the data is peanut butter', done => {
  function callback(data) {
    expect(data).toBe('peanut butter');
    done();
  }

  fetchData(callback);
});
```

### Promise
Promise를 반환하는 비동기함수일 경우 해당함수를 test내에서 반환하게 되면 완료 콜백내 expectation구문까지 테스트가 종료되지 않고 기다린다.
```js
test('the data is peanut butter', () => {
  expect.assertions(1);                     // 테스트 종료까지 나올 expectation의 개수를 미리 지정해주어야 테스트가 그 갯수만큼 검사하고 종료된다.
  return fetchData().then(data => {
    expect(data).toBe('peanut butter');
  });
});
```

```js
test('the fetch fails with an error', () => {
  expect.assertions(1);
  return fetchData().catch(e => expect(e).toMatch('error'));
});
```

### .resolves / .rejects
Promise가 성공할 때까지 테스트는 대기한다.
```js
test('the data is peanut butter', () => {
  expect.assertions(1);
  return expect(fetchData()).resolves.toBe('peanut butter');
});
```
테스트가 실패할 것으로 예상되면 rejects를 사용할 수 있다. 성공시 테스트는 실패한다.
```js
test('the fetch fails with an error', () => {
  expect.assertions(1);
  return expect(fetchData()).rejects.toMatch('error');
});
```

### Async/Await
async를 사용할 수도 있다. 테스트 인자함수에 async를 명시한다.
```js
test('the data is peanut butter', async () => {
  expect.assertions(1);
  const data = await fetchData();
  expect(data).toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  expect.assertions(1);
  try {
    await fetchData();
  } catch (e) {
    expect(e).toMatch('error');
  }
});
```
```js
test('the data is peanut butter', async () => {
  expect.assertions(1);
  await expect(fetchData()).resolves.toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  expect.assertions(1);
  await expect(fetchData()).rejects.toMatch('error');
});
```

## Hooks
```
beforeAll(() => console.log('1 - beforeAll'));
afterAll(() => console.log('1 - afterAll'));
beforeEach(() => console.log('1 - beforeEach'));
afterEach(() => console.log('1 - afterEach'));
test('', () => console.log('1 - test'));
describe('Scoped / Nested block', () => {
  beforeAll(() => console.log('2 - beforeAll'));
  afterAll(() => console.log('2 - afterAll'));
  beforeEach(() => console.log('2 - beforeEach'));
  afterEach(() => console.log('2 - afterEach'));
  test('', () => console.log('2 - test'));
});

// 1 - beforeAll
// 1 - beforeEach
// 1 - test
// 1 - afterEach
// 2 - beforeAll
// 1 - beforeEach
// 2 - beforeEach
// 2 - test
// 2 - afterEach
// 1 - afterEach
// 2 - afterAll
// 1 - afterAll
```
### 충돌 예방
before로 예상치 못한 방해로 인해 테스트가 실패할 경우가 있다.

그럴경우 `test.only` 메서드로 현재 테스트하고 있는 테스트만 돌릴수 있다.

```
test.only('this will be the only test that runs', () => {
  expect(true).toBe(false);
});

test('this test will not run', () => {
  expect('A').toBe('A');
});
```

## mock 함수 사용
### 기본 사용법
`jest.fn(x => 42 + x);` jest.fn()으로 만든다

```js
import {foreach} from '../modules/foreach';

const mockCallback = jest.fn(x => 42 + x);

it('mock test', () => {
    foreach([0, 1], mockCallback);
    
    // 목킹 함수가 두번 호출되었다.
    expect(mockCallback.mock.calls.length).toBe(2);

    // 목킹 함수의 첫번째 호출의 첫번째 인자는 0이다.
    expect(mockCallback.mock.calls[0][0]).toBe(0);

    // 목킹 함수의 두번째 호출의 첫번째 인자는 1이다.
    expect(mockCallback.mock.calls[1][0]).toBe(1);

    // 목킹 함수의 첫번째 호출 리턴값은 42다.
    expect(mockCallback.mock.results[0].value).toBe(42);

    // 목킹 함수의 두번째 호출 리턴값은 42다.
    expect(mockCallback.mock.results[1].value).toBe(43);
})
```

### return value mocking
```js
const myMock = jest.fn();
console.log(myMock());
// > undefined

myMock
  .mockReturnValueOnce(10)
  .mockReturnValueOnce('x')
  .mockReturnValue(true);

console.log(myMock(), myMock(), myMock(), myMock());
// > 10, 'x', true, true
```
### promise resolved value moking
```js
// users.js
import axios from 'axios';

class Users {
  static all() {
    return axios.get('/users.json').then(resp => resp.data);
  }
}

export default Users;
```

```js
// users.test.js
import axios from 'axios';
import Users from './users';

jest.mock('axios');

test('should fetch users', () => {
  const users = [{name: 'Bob'}];
  const resp = {data: users};
  axios.get.mockResolvedValue(resp);

  // or you could use the following depending on your use case:
  // axios.get.mockImplementation(() => Promise.resolve(resp))

  return Users.all().then(data => expect(data).toEqual(users));
});
```

### mocking 직접 구현
fn 내부 구현
```js
const myMockFn = jest.fn(cb => cb(null, true));

myMockFn((err, val) => console.log(val));
// > true
```
mockImplementation
```js
// foo.js
module.exports = function() {
  // some implementation;
};

// test.js
jest.mock('../foo'); // this happens automatically with automocking
const foo = require('../foo');

// foo is a mock function
foo.mockImplementation(() => 42);
foo();
// > 42
```

mockImplementationOnce 활용 default 처리
```js
const myMockFn = jest
  .fn(() => 'default')
  .mockImplementationOnce(() => 'first call')
  .mockImplementationOnce(() => 'second call');

console.log(myMockFn(), myMockFn(), myMockFn(), myMockFn());
// > 'first call', 'second call', 'default', 'default'
```

channing mocking을 위한 this 구성
```js
const myObj = {
  myMethod: jest.fn().mockReturnThis(),
};

// is the same as

const otherObj = {
  myMethod: jest.fn(function() {
    return this;
  }),
};
```

mock name 설정
```js
const myMockFn = jest
  .fn()
  .mockReturnValue('default')
  .mockImplementation(scalar => 42 + scalar)
  .mockName('add42');
```

custome machers
```js
it('custom matcher', () => {
    const mockFunc = jest.fn();
    mockFunc();
    mockFunc(1,2);
    mockFunc(3,4);

    expect(mockFunc).toHaveBeenCalled();       // 적어도 한번 호출되었다.
    expect(mockFunc).toHaveBeenCalledWith(1, 2);    // 1, 2 인자로 적어도 한번 호추되었다.
    expect(mockFunc).toHaveBeenLastCalledWith(3, 4);  // 마지막 호출은 3, 4인자로 호출되었다.
    expect(mockFunc).toMatchSnapshot(); // 목함수 호출 스냅샷
})
```

## 비대칭 매처
```js
it('will check the matchers and pass', () => {
    const user = {
        createdAt: new Date(),
        id: Math.floor(Math.random() * 20),
        name: 'LeBron James',
    };

    expect(user).toMatchSnapshot({
        createdAt: expect.any(Date),
        id: expect.any(Number),
    });
});
```
> 스냅샷 테스트시 해당 프로퍼티는 비대칭 매퍼로 적용하면 테스트가 실패하지 않는다.
### 종류
- Date: expect.any(Date)
- Number: expect.any(Number)

