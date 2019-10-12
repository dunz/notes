# RxJS Beyond the Basics: Operators in Depth

## Understand RxJS Operators
> RxJS 연산자 이해
> 
> Observable.create의 기본 사항 및 기타 생성 기능에 대해 살펴 보았습니다. 이제이 과정의 초점이되는 연산자를 살펴 보도록하겠습니다. 연산자가 Observable 유형에 첨부 된 단순 순수 함수 인 방법을 살펴 보겠습니다.

```js
import { of, from, interval, Observable } from "rxjs";
import { map, take, combineAll } from "rxjs/operators";

const source$ = of(1, 2, 3, 4, 5);

const mul = function (mul) {
  const source = this;
  return Observable.create(observable => {
    source.subscribe(
      x => observable.next(x * mul),
      err => observable.error(err),
      complete => observable.complete()
    );
  });
};

Observable.prototype.mul = mul;

const result$ = source$.mul(100);

result$.subscribe(
  x => {
    console.log(`next ${x}`);
  },
  console.log,
  (c) => {
    console.log(`done ${c}`);
  }
);
````

## Use RxJS mapTo and map to Transform Values Emitted by Observables
> 옵저버 블이 방출 한 값을 변환합니다. 우리는 multiplyBy라는 첫 번째 연산자를 만들었습니다.이 연산자는 약간 유용 해 보이지만 실제로는 너무 구체적이기 때문에 필요하지 않습니다. 숫자에 대한 단순한 곱셈 만 수행합니다. 이 단원에서는 제공된 값을 계산하는 데 map () 연산자가 어떻게 유용한 지 살펴 봅니다.

```js
import { of, from, interval, Observable } from "rxjs";
import { map, mapto, take, combineAll } from "rxjs/operators";

const source$ = interval(1000);

const result$ = source$.pipe(map(x => x * 10));

result$.subscribe(
  x => {
    console.log(`next ${x}`);
  },
  console.log,
  c => {
    console.log(`done ${c}`);
  }
);
```

