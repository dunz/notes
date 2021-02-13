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
> - 두 번째 자리 숫자부터 뽑아서 그 숫자가 첫 숫자보다 크면 첫 숫자 오른쪽에, 작으면 왼쪽에 넣습니다.
> - 세 번째 자리 숫자를 뽑아서 앞의 두 숫자와 크기를 비교해서 알맞은 자리에 넣습니다.
> - 이렇게 끝까지 계속하면 정렬된다.
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

## 합병 정렬 - Merge Sort
```js
const mergeSort = function(array) {
  if (array.length < 2) return array; // 원소가 하나일 때는 그대로 내보냅니다.
  var pivot = Math.floor(array.length / 2); // 대략 반으로 쪼개는 코드
  var left = array.slice(0, pivot); // 쪼갠 왼쪽
  var right = array.slice(pivot, array.length); // 쪼갠 오른쪽
  return merge(mergeSort(left), mergeSort(right)); // 재귀적으로 쪼개고 합칩니다.
}

function merge(left, right) {
  var result = [];
  while (left.length && right.length) {
    if (left[0] <= right[0]) { // 두 배열의 첫 원소를 비교하여
      result.push(left.shift()); // 더 작은 수를 결과에 넣어줍니다.
    } else {
      result.push(right.shift()); // 오른쪽도 마찬가지
    }
  }
  while (left.length) result.push(left.shift()); // 어느 한 배열이 더 많이 남았다면 나머지를 다 넣어줍니다.
  while (right.length) result.push(right.shift()); // 오른쪽도 마찬가지
  return result;
};

mergeSort([5,2,4,7,6,1,3,8]); // [1, 2, 3, 4, 5, 6, 7, 8]
```

## 버블 정렬 O(n^2) - Bubble Sort
```js
const bubbleSort = function(array) {
  const length = array.length;
  let i, j, temp;
  for (i = 0; i < length - 1; i++) { // 순차적으로 비교하기 위한 반복문
    for (j = 0; j < length - 1 - i; j++) { // 끝까지 돌았을 때 다시 처음부터 비교하기 위한 반복문
      if (array[j] > array[j + 1]) { // 두 수를 비교하여 앞 수가 뒷 수보다 크면
        temp = array[j]; // 두 수를 서로 바꿔준다
        array[j] = array[j + 1];
        array[j + 1] = temp;
      }
    }
  }
  return array;
};

bubbleSort([5,1,7,4,6,3,2,8]);
```

## 선택 정렬 O(n^2) - Selection Sort
```js
const selectionSort = function(array) {
  const length = array.length;
  let minIndex, temp, i, j;
  for (i = 0; i < length - 1; i++) { // 처음부터 훑으면서
    minIndex = i;
    for (j = i + 1; j < length; j++) { // 최솟값의 위치를 찾음
      if (array[j] < array[minIndex]) {
        minIndex = j;
      }
    }
    temp = array[minIndex]; // 최솟값을 저장
    array[minIndex] = array[i];
    array[i] = temp; // 최솟값을 제일 앞으로 보냄
  }
  return array;
};

selectionSort([5,1,4,7,2,6,8,3]); // [1,2,3,4,5,6,7,8]
```

## 퀵 정렬 O(nLogn) ~ O(n^2) - Quick Sort
```js
var partition = function(array, left, right, pivotIndex) { // 정렬하는 부분
  
  var temp;
  var pivot = array[pivotIndex];
  while (left <= right) { // 왼쪽, 오른쪽 수를 규칙과 비교해 다음 수로 넘어갑니다.
    // console.log([...array]);
    while (array[left] < pivot)
      left++;
    while (array[right] > pivot)
      right--;
    
    if (left <= right) { // 왼쪽이 기준보다 크고, 오른쪽이 기준보다 작으면
      temp = array[left];
      array[left] = array[right];
      array[right] = temp; // 서로 바꿔줍니다.
      left++;
      right--;
    }
  }
  temp = array[left];
  array[left] = array[pivotIndex];
  array[pivotIndex] = temp; // 마지막으로 기준과 만난 수를 바꿔줍니다. 기준의 위치는 이제 i입니다.
  
  return left;
};

var quickSort = function(array, left, right) { // 재귀하는 부분
  if (!left) left = 0;
  if (!right) right = array.length - 1;
  var pivotIndex = right; // 배열 가장 오른쪽의 수를 기준으로 뽑습니다.
  pivotIndex = partition(array, left, right - 1, pivotIndex); // right - 1을 하는 이유는 기준(현재 right)을 제외하고 정렬하기 위함입니다.
  if (left < pivotIndex - 1)
    quickSort(array, left, pivotIndex - 1); // 기준 왼쪽 부분 재귀
  if (pivotIndex + 1 < right)
    quickSort(array, pivotIndex + 1, right); // 기준 오른쪽 부분 재귀
  return array;
};

quickSort([4,1,7,6,3,8,2,5]); // [1,2,3,4,5,6,7,8]
```

## 계수 정렬 O(n) ~ O(무한) - Counting Sort
```js
var countingSort = function(array, k) {
  var count = [], result = [];
  for (var i = 0; i <= k; i++) { // 모든 숫자의 개수를 일단 0으로 초기화합니다.
    count[i] = 0;
  }
  for (var j = 0; j < array.length; j++) { // 숫자의 개수를 세어 저장합니다.
    count[array[j]] += 1;
  }
  for (i = 0; i < k ; i++) { // 누적합을 구합니다.
    count[i + 1] += count[i];
  }
  for (j = 0; j < array.length; j++) { // 누적합이 가리키는 인덱스를 바탕으로 결과에 숫자를  집어넣습니다.
    result[count[array[j]] - 1] = array[j];
    count[array[j]] -= 1;
  }
  return result;
};

// 배열에 큰 수가 들어갈 수록 메모리를 많이 잡아먹기 때문에 좋지 않습니다.
countingSort([3,4,0,1,2,4,2,4], 4); // [0,1,2,2,3,4,4,4]
```
## 기수 정렬 O(kn)- Radix Sort
```js
const counter = [];
const radixLSD = function(array, d) {
  let mod = 10;
  for (var i = 0; i < d; i++, mod *= 10) { // mod는 현재 정렬 중인 자리수를 나타내는 것으로 10부터 해서 100, 1000, ...으로 커집니다.
    for (var j = 0; j < array.length; j++) {
      var bucket = parseInt(array[j] % mod); // 같은 그룹으로 묶일 나머지를 나타내는 부분입니다.
      if (counter[bucket] == null ) {
        counter[bucket] = [];
      }
      counter[bucket].push(array[j]); // 나머지 별로 묶어줍니다.
    }
    // console.log(counter.slice(0));
    var pos = 0;
    for (var j = 0; j < counter.length; j++) { // counter에 저장한 묶음들(나머지 순서로 정렬됨)을 실제 배열에 반영해줍니다.
      var value = null ;
      if (counter[j] != null ) {
        while ((value = counter[j].shift()) != null ) {
          array[pos++] = value;
        }
      }
    }
  }
  return array;
}

radixLSD([125,383,274,96,0,9,81,72], 3); // [0,9,72,81,96,125,274,383]
```

## 단일 연결 리스트 - Single Linked List
```js
class LinkedList {
  length = 0;
  head = null;

  constructor(data) {
    if (![null, undefined].includes(data)) {
      this.head = new Node(data);
      this.length++;
    }
  }
  add(data) {
    const node = new Node(data);
    let cursor = this.head;

    if (!cursor) {
      this.head = node;
    } else {
      while (cursor.next) {
        cursor = cursor.next;
      }
      cursor.next = node;
    }

    this.length++;
    return node;
  }
  search(position) {
    let cursor = this.head;
    for (let i = 0; i < position; i++) {
      if (!cursor.next) break;
      cursor = cursor.next;
    }
    return cursor.data;
  }
  remove(position) {
    let cursor = this.head,
      prev;

    if (position <= 0) {
      this.head = cursor.next;
    } else {
      for (let i = 0; i < position; i++) {
        if (!cursor.next) break;
        prev = cursor;
        cursor = cursor.next;
      }
      prev.next = cursor.next;
    }

    this.length--;
    return cursor;
  }
}
class Node {
  data: null;
  next: null;
  constructor(data) {
    this.data = data;
    this.next = null;
  }
}

var list = new LinkedList();
console.log(list);
list.add(1);
list.add(2);
list.add(3);
list.length; // 3
list.search(0); // 1
list.search(2); // 3
list.remove(1);
list.length; // 2
```

## 스택 - Stack
```js
class Stack {
  top = null;
  count = 0;

  push(data) {
    const node = new Node(data);
    node.next = this.top;
    this.top = node;
    return ++this.count;
  }
  pop() {
    if (!this.top) return false;
    const { data, next } = this.top;
    this.top = next;
    this.count--;
    return data;
  }
  stackTop() {
    return this.top?.data;
  }
}
class Node {
  data = null;
  next = null;
  
  constructor(data) {
    this.data = data;
    this.next = null;
  }
}

var stack = new Stack();
console.log(stack);
stack.push(1); // 1
stack.push(3); // 2
stack.push(5); // 3
stack.pop(); // 5
stack.stackTop(); // 3
```

## 큐 - Queue
```js
class Queue {
  count = 0;
  head = null;
  rear = null;

  enqueue(data) {
    const node = new Node(data);
    if (!this.head) {
      this.head = node;
    } else {
      this.rear.next = node;
    }
    this.rear = node;
    return ++this.count;
  }
  dequeue() {
    if (!this.head) return false;
    const { data, next } = this.head;
    this.head = next;
    this.count--;
    return data;
  }
  front() {
    return this.head?.data;
  }
}
class Node {
  data = null;
  next = null;
  constructor(data) {
    this.data = data;
    this.next = null;
  }
}

var queue = new Queue();
console.log(queue);
queue.enqueue(1); // 1
queue.enqueue(3); // 2
queue.enqueue(5); // 3
queue.dequeue(); // 1
queue.front(); // 3
```

## 이진 검색 트리 - Binary Search Tree (BST)
```js
class Tree {
  count = 0;
  root = null;
  _add(root, node) {
    if (!root) return node;
    const { data } = node;
    if (data < root.data) {
      root.left = this._add(root.left, node);
    } else {
      root.right = this._add(root.right, node);
    }
    return root;
  }
  add(data) {
    const node = new Node(data);
    if (!this.root) {
      this.root = node;
    } else {
      this._add(this.root, node);
    }
    return ++this.count;
  }
  _remove(root, data) {
    let newRoot, exchange, temp;
    if (!root) return false;
    if (data < root.data) {
      root.left = this._remove(root.left, data);
    } else if (data > root.data) {
      root.right = this._remove(root.right, data);
    } else {
      if (!root.left) {
        return root.right;
      } else if (!root.right) {
        return root.left;
      } else {
        exchange = root.left;
        while (exchange.right) {
          exchange = exchange.right;
        }
        temp = root.data;
        root.data = exchange.data;
        exchange.data = temp;
        root.left = this._remove(root.left, exchange.data);
      }
    }
    return root;
  }
  remove(data) {
    const node = this._remove(this.root, data);
    if (node) {
      this.root = node;
      if (--this.count === 0) {
        this.root = null;
      }
      return true;
    }
    return this;
  }
}

class Node {
  data = null;
  left = null;
  right = null;

  constructor(data) {
    this.data = data;
  }
}

var tree = new Tree();
console.log(tree);
tree.add(5); // 1
tree.add(3); // 2
tree.add(4); // 3
tree.add(2); // 4
tree.add(7); // 5
tree.add(6); // 6
tree.root.left.data; // 3
tree.root.left.left.data; // 2;
tree.root.left.right.data; // 4
tree.remove(3); // true
tree.root.left.data; // 2
```
