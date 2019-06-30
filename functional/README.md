# FunctionalJS

### _each 함수
```js
const _each = (list, iteratee) => {
    for(let i = 0, ; i< list.length; i++) {
        iteratee(list[i]);
    }
    return list;
}
```

### _filter 함수
```js
const _filter = (list, predicate) => {
    const newList = [];
    _each(list, val => {
        if(predicate(val)) newList.push(val);
    });
    return newList;
}

const filter = curry(function* (f, iter) {
	for (const a of iter) {
		if(f(a)) yield a;
	}
});
```

### _map 함수
```js
const _map = (list, mapper) => {
    const newList = [];
    _each(list, (val, key) => {
        newList.push(mapper(val, key));
    });
    return newList;
}

const map = curry(function* (f, iter) {
	for (const a of iter) {
		yield f(a);
	}
});
```

### take함수
```js
const take = curry(function (length, iter) {
	let res = [];
	for (const a of iter){
		if(res.length >= length) return res;
		res.push(a);
		
	}
	return res;
})

const takeWhile = curry(function (f, iter) {
	let res = [];
	for (const a of iter){
		if(!f(a)) return res;
		res.push(a);
		
	}
	return res;
})
```

### range함수
```js
const range = function* (stop) {
	let i = -1;
	while(++i < stop) yield i;
}
```

### flat 함수
```js
function* flat(iter){
	for(const a of iter) {
		if(a && a[Symbol.iterator]) yield* a;
		else yield a;
	}
}
```

### _curry 함수
```js
const _curry = fn => (a, b) => arguments.length === 2 ? fn(a, b) : b => fn(a, b);

const _curryr = fn => (a, b) => arguments.length === 2 ? fn(a, b) : b => fn(b, a);

const curry = f => (a, ...bs) => bs.length ? f(a, ...bs) : (...bs) => f(a, ...bs);
```
> 본체함수의 실행을 모든 인자를 받을때까지 그 실행시점을 늦추는 함수

### _get함수
```js
const _get=_curryr((obj, key) => obj === null ? undefined : obj[key])
```
> 객체에서 에러 예외처리후 해당 프로퍼티의 값을 가져오는 함수

### _get과 _curryr 함수 응용
```js
const users = [{name:'동주'}, {name:'GD'}, {name:'승리'}];
_get(users[0], 'name'); // 동주
_get('name')(users[1]); // GD
_get('name')(users[2]); // 승리
```

### _reduce 함수
```js
const _reduce = (list, iter, memo) => {
    if(arguments.length ===2) {
        memo = list[0];
        list = _rest(list);
    }
    _each(list, val => {
        memo = iter(memo, val);
    });
    return memo;
}

const reduce = curry(function (f, acc, iter){
	if(arguments.length ===2) {
		iter = acc[Symbol.iterator]();
		acc = iter.next().value;
	}
	for (const a of iter) {
		acc = f(acc, a);
	}
	return acc;
})
```
> 배열을 순회하며 최종적으로 다른 자료구조를 얻고자 할때 사용

### _rest 함수
```js
const _rest = (list, num) => {
    return Array.prototype.slice.call(list, num);
}
```
> num의 index 만큼 자른후 반환하는 함수, array-like 객체에서도 사용가능

### _pipe 함수
```js
const _pipe = () => {
    let fns = arguments;
    return arg => _reduce(fns, (arg, fn) => fn(arg), arg)
};
```
> 함수들을 인자로 받아서 이 함수를 연속적으로 실행해주는 함수를 반환

### _go 함수
```js
const _go = arg => {
    const fns = _rest(arguments, 1);
    return _pipe.apply(null, fns)(arg);
}

const go = (...as) => reduce((a, f) => f(a), as)

// Promise를 값으로 다루어 대응할 수 있다. (모나드)
const go = (a, f) => a instanceof Promise ? a.then(f) : f(a);
```
> 즉시 실행되는 파이프 함수로 첫번째로 인자를 받고 두번째부터 함수를 받음

### map, filter을 curryr 로써 활용
```js
    const _map = _curryr(_map);
    _map([1, 2, 3], val => val * 2);
    or
    _map(val => val * 2)([1, 2, 3])
    
    // 간편화
    _go(users,
        users => _filter(users, user => user.age >= 30),
        users => _map(users, _get('name')),
        console.log
    ) // 이거를
    _go (users,
        _filter(user => user.age >= 30),
        _map(_get('name')),
        console.log
    )   // 이렇게 간단하게 변경 가능 !!! 혁명!
    // 커리함수의 엄청난 활용성!!!!!!!!!!!!!!! 커리 함수 사랑해요!
```

### each함수에 null넣어도 에러 않나게 처리하기
```js
    const _get = _curryr(_get);
    const _length = _get('length');
    const _each = (list, iter) => {
        for (var i=0; len = _length(list); i < len; i++) {
            iter(list[i]);
        }
        return list;
    }
    _each(null, console.log);
```

### _isObject 함수
```js
const _isObject = obj => !!obj && typeof obj === 'object'
```

### _keys 함수
```js
const _keys = obj => _isObject(obj) ? Object.keys(obj) : [];
```

### each 함수 키벨류 쌍인 데이터 대응
```js
const _each = (list, iter) => {
    const keys = _keys(list);
    for(let i = 0, len = keys.length; i < len; i++) {
        iter(list[keys[i]], keys[i]);
    }
    return list;
}
```

### _identity 함수
```js
const _identity = val => val;
```

### _values 함수
```js
const _values = data => _map(data, val => val); or
const _values = data => _map(data, _identity);
const _values = _map(_identity) // _map 함수가 curryr로 생성하였을때 가능
```
> data 배열이 들어오게되면 변화가 없지만 객체가 들어오게 되면 프로퍼티별 해당 값이 반환됨

### _pluck 함수
```js
const _pluck = (data, key) => _map(data, obj => obj[key]); or
const _pluck = (data, key) => _map(data, _get(key));
```
> 해당키로만 이루어진 배열 반환

### _reject 함수
```js
const _reject = (data, predi) => _filter(data, val => !predi(val)); or
const _reject = (data, predi) => _filter(data, _negate(predi));
```
> 거르기 함수 filter의 반대동작 predicate 의 반환값이 true 것들을 걸러주는 함수

### _negate 함수
```js
const _negate = func => val => !fun(val);
```
> 선언당시 받은 인자의 함수를 사용시점에 받은 인자를 대입해 실행한 값의 역치를 반환

### _compact 함수
```js
const _compact = _filter(_identity);
_compact([1, 2, 0, false, null, {}])    // 1, 2, {}
```
> truty 데이터만 남기는 함수

### _find 함수
```js
const _find = (list, predi) => {
    const keys = _keys(list);
    for(let i = 0, len = keys.length; i < len; i++) {
        let val = list[keys[i]];
        if (predi(val)) return val;
    }
}
```
> 첫번째로 true 에 해당하는 값을 만날경우 그 객체 하나를 반환하는 함수

### _findIndex 함수
```js
const _findIndex = (list, predi) => {
    const keys = _keys(list);
    for(let i = 0, len = keys.length; i < len; i++) {
        if (predi(list[keys[i]])) return i;
    }
    return -1;
}
```
> 첫번째로 true 에 해당하는 값을 만날경우 그 객체의 index 하나를 반환하는 함수

### _some 함수
```js
const _some = (data, predi) => _findIndex(data, predi || _identity) >= 0;
_some([1, 2, 5, 10, 20], val => val > 10);  // true
```
> 조건에 만족하는 값이 하나라도 있으면 true를 반환하는 함수

### _every 함수
```js
const _every = (data, predi) => _findIndex(data, _negate(predi || _identity)) === -1;
_some([1, 2, 5, 10, 20], val => val >= 1);  // true
```
> 모든 조건이 만족해야 true를 반환하는 함수\

### _min 함수
```js
const _min = data => _reduce(data, (a, b) => a < b ? a : b)
```

### _max 함수
```js
const _max = data => _reduce(data, (a, b) => a > b ? a : b)
```

### _minBy 함수
```js
const _minBy = (data, iter) => _reduce(data, (a, b) => iter(a) < iter(b) ? a : b)
_minBy([1, 2, 4, 10, 5, -4], Math.abs);     // 1
```
> 비교구문에서 쓰일 데이터 가공 조건을 추가적으로 넘겨줌

### _maxBy 함수
```js
const _maxBy = (data, iter) => _reduce(data, (a, b) => iter(a) > iter(b) ? a : b)
_maxBy([1, 2, 4, 10, 5, -4, -11], Math.abs);     // -11
_maxBy(users, user => user.age)     // 가장 나이가 많은 객체 추출

// curryr 응용 _minBy curryr 로 생성
_go(users,
    _filter(user => user.age >= 30),
    _minBy(_get('age')),
    console.log
)
```
> 비교구문에서 쓰일 데이터 가공 조건을 추가적으로 넘겨줌

### _push 함수
```js
const _push = (obj, key, val) => {
    (obj[key] = obj[key] || []).push(val)
    return obj;
};
```
> 키가 있으면 그곳에 push 하고 없으면 빈배열에 push

### _head 함수
```js
const _head = list => list[0];
```

### _groupBy 함수
```js
const _groupBy = _curryr((data, iter) => _reduce(data, (grouped, val) => _push(grouped, iter(val), val), {}));

_go(users,
    _groupBy(user => user.age),
    console.log
);

_go(users,
    _groupBy(user => user.age - user.age % 10),
    console.log
)       // 10대, 20대, 30대 별 groupBy

_go(users,
    _groupBy(_pipe(_get('name'), _head)),
    console.log
)
```
> 객체를 하나의 키로 그룹화

### _inc 함수
```js
const _inc = (obj, key) => {
    obj[key] ? obj[key]++ : obj[key] = 1;
    return obj;
};
```
> 키가 있으면 그 값을 증가시키고 없으면 1로 할당

### _countBy 함수
```js
const _countBy = _curryr((data, iter) => _reduce(data, (grouped, val) => _inc(grouped, iter(val)), {}));

_go(users,
    _countBy(user => user.age - user.age % 10),
    console.log
)       // 10대, 20대, 30대 별 groupBy
```
> 객체를 하나의 키로 그룹화되는 갯수로 설정

### _pairs 함수
```js
const _pairs = _map((val, key) => [key, val]);
_pairs(users[0]);
```
### 응용
```js
_go(users,
    _reject(user => user.age < 20),
    _countBy(user => user.age - user.age % 10),
    _map((count, key) => `<li>${key}대는 ${count}명 입니다.</li>`),
    list => `<ul>${list.join('')}</ul>`,
    document.write.bind(document)
);

const f1 = _pipe(
    _countBy(user => user.age - user.age % 10),
    _map((count, key) => `<li>${key}대는 ${count}명 입니다.</li>`),
    list => `<ul>${list.join('')}</ul>`,
    document.write.bind(document)
);

f1(users);

const f2 = _pipe(
    _reject(user => user.age < 20),
    f1
);

f2(users);

_go(users, _reject(user => user.age < 20), f1);
_go(users, _filter(user => user.age < 20), f1);
```