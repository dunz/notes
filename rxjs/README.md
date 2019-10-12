# RxJS

## tap
> 옵저버블 파이프라인에서 옵저버블 스트림에 영향을 미치지 않는 연산자
```js
const source$ = interval(1000).take(4);

const bar$ = source$.pipe(
  tap((x) => 'ASDF'),
)

bar$.subscribe(console.log)
// 0
// 1
// 2
// 3
```

## map
> 다음 파이프라인에 들어가는 데이터 변환
```js
const source$ = interval(1000).take(4);

const bar$ = source$.pipe(
  tap((x) => 'console.log('before ' + x)'),
  map(x => x*2),
  tap(x => console.log('after ' + x))
)

bar$.subscribe(console.log)
// before 0
// after 0
// 0
// before 1
// after 2
// 2
// ... 
```

## skip
> 해당 INDEX 전 까지 스킵
```js
const source$ = interval(1000).take(4);

const bar$ = source$.pipe(
  skip(2),
)

bar$.subscribe(console.log)
// 2
// 3
```

## takeLast
> 내려오는 마지막 데이터에 인자만큼만 내려옴
```js
const source$ = interval(1000).take(4);

const bar$ = source$.pipe(
  takeLast(2),
)

bar$.subscribe(console.log)
// 2
// 3
```

## first, last
> 첫번째 데이터만 내려옴
```js
const source$ = interval(1000).take(4);

const bar$ = source$.pipe(
  first(),
)

bar$.subscribe(console.log)
// 0
```

## first, last
> 마지막 데이터만 내려옴
```js
const source$ = interval(1000).take(4);

const bar$ = source$.pipe(
  last(),
)

bar$.subscribe(console.log)
// 3
```

## concat
> 옵저버블 합침
```js
const source$ = interval(1000).take(4);

const prefix$ = of('prefix');
const bar$ = prefix$.pipe(
  concat(source$)
)


bar$.subscribe(console.log)
// prefix
// 1
// 2
// 3
// 4
```

## startWith
> 초기 스트림을 넣어줌, prefix 기능으로 사용 가능
```js
const source$ = interval(1000).take(4);

const bar$ = source$.pipe(
  startWith('prefix')
)

bar$.subscribe(console.log)
// prefix
// 1
// 2
// 3
// 4
```

## merge
> 병렬로 두개의 옵저저블 병합
```js
const foo$ = interval(500).pipe(take(4));
const bar$ = interval(300).pipe(take(5));

const result$ = foo$.pipe(merge(bar$));

result$.subscribe(console.log)
// 0
// 0
// 1
// 2
// 1
// 3
// 2
// 4
// 3
```

## combineLatest
> 각 옵저버블이 결합되어 각 옵저버블의 인자값을 받아와서 처리
```js
const foo$ = interval(500).pipe(take(4));
const bar$ = interval(300).pipe(take(5));

const result$ = foo$.pipe(combineLatest(bar$, (x, y) => x+y));

result$.subscribe(console.log)
// 0
// 1
// 2
// 3
// 4
// 5
// 6
// 7
```

## withLatestFrom
> 소스 Observable을 다른 Observable과 결합하여 소스가 방출 될 때만 각각의 최신 값에서 값을 계산하는 Observable을 만듭니다.
```js
const foo$ = zip(
  interval(500).pipe(
    take(5)
  ),
  of("H", "e", "l", "l", "o")
).pipe(map(([i, o]) => o));

const bar$ = zip(
  interval(300).pipe(
    take(7)
  ),
  of(0, 1, 0, 1, 0, 1, 0)
).pipe(map(([i, o]) => o))

const result$ = foo$.pipe(
  withLatestFrom(bar$, (c, n) =>
    n === 1 ? c.toUpperCase() : c.toLowerCase()
  )
);

result$.subscribe(console.log);
// 'h'
// 'e'
// 'l'
// 'L'
// 'o'
```

## scan
> 자바스크립트 reduce와 같은 기능
```js
const foo$ = of('h', 'e', 'l', 'l', 'o');
const bar$ = interval(600).pipe(take(5));

const result$ = zip(foo$, bar$, (x,y) => x).pipe(scan((acc, x) => acc+x, ''))

result$.subscribe(console.log);
```

## bufferCount
> 옵저버블을 해당 카운트만큼 묶어서 값을 내려줌
```js
const foo$ = of('h', 'e', 'l', 'l', 'o');
const bar$ = interval(600).pipe(take(5));

const result$ = zip(foo$, bar$, (x,y) => x).pipe(
  bufferCount(2)
)
```