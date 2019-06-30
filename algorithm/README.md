# 알고리즘

## 최대공약수 구하기

```js
// 단순한 포문 돌기(시간복잡도가 높다)
let a = 15;
let b = 20;

for(let i=15; i>=1; i--) {
  console.log(i);       // 11번의 포문
  if (a % i === 0 && b % i === 0) {
    console.log(i); // <= 최대공약수 5 
    break;
  }
}

// 유클리드 호제법
// f(a, b) = f(b, a mod b)이 성립한다

const func = (a, b) => {
  const mod = a%b;
  console.log(1) // 총 세번의 재귀호출
  return mod === 0 ? b : func(b, mod) // <= mod가 0이 되는 시점에 b가 최대공약수가 된다 5 리턴
} 

func(15, 20)
```