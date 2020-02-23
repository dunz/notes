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
```sh
yarn add -D babel-jest @babel/core @babel/preset-env
```
```js
module.exports = {
  presets: [
    [
      '@babel/preset-env',
      {
        targets: {
          node: 'current',
        },
      },
    ],
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
            [
                '@babel/preset-env',
                {
                    targets: {
                        node: 'current',
                    },
                },
            ],
        ],
    };
}
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
일반적으로 비동기 코드를 실행하기 전에 테스트가 끝나버린다.

비동기 완료 콜백 실행 후까지 테스트를 미루기 위해 test함수의 인자값으로 done을 추가하여 비동기 콜백에서 테스트 완료 후 done함수를 실행해주어야 한다.

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
