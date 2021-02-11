# 알고리즘

## 최대공약수

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

## 삽입정렬 O(n^2) - Insertion Sort
```js
const insertionSort = function(array) {
  let i = 1, j, temp;
  for (i; i < array.length; i++) {
    temp = array[i]; // 새로운 숫자를 선택함
    for (j = i - 1; j >= 0 && temp < array[j]; j--) { // 선택한 숫자를 이미 정렬된 숫자들과 비교하며 넣을 위치를 찾는 과정, 선택한 숫자가 정렬된 숫자보다 작으면
      array[j + 1] = array[j]; // 한 칸씩 뒤로 밀어낸다
    }
    array[j + 1] = temp; // 마지막 빈 칸에 선택한 숫자를 넣어준다.
  }
  return array;
};

insertionSort([5, 6, 1, 2, 4, 3]); // [1, 2, 3, 4, 5, 6]
```
