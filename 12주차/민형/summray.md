# 스프레드 문법

- ES6에서 도입된 스프레드 문법은 하나로 뭉쳐 있는 여러 값들의 집합을 펼쳐서 개별적인 값들의 목록으로 만든다.
- 스프레드 문법을 사용할 수 있는 대상은 for...of 문으로 순회할 수 있는 이터러블에 한정된다.

```js
// ...[1, 2, 3]은 [1, 2, 3]을 개별 요소로 분리한다.(-> 1, 2, 3)
console.log(...[1, 2, 3]); // 1 2 3

// 문자열은 이터러블이다.
console.log(...'Hello'); // H e l l o

// Map과 Set은 이터러블이다.
console.log(
  ...new Map([
    ['a', '1'],
    ['b', '2'],
  ]);
) // [ 'a', '1' ] [ 'b', '2' ]
console.log(...new Set([1, 2, 3])); // 1 2 3

// 이터러블이 아닌 일반 객체는 스프레드 문법의 대상이 될 수 없다.
console.log(...{ a: 1, b: 2 }); // TypeError
```

- 스프레드 문법의 결과 자체는 값이 아니기 때문에 변수에 할당할 수 없다.

```js
// 스프레드 문법의 결과는 값이 아니다.
const list = ...[1, 2, 3]; // SyntaxError
```

## 함수 호출문의 인수 목록에서 사용하는 경우

```js
var arr = [1, 2, 3];

var max = Math.max.apply(null, arr); // 3

// 스프레드 문법을 사용한다면
const arr = [1, 2, 3];

const max = Math.max(...arr); // 3
```

- 스프레드 문법은 Rest 파라미터와 형태가 동일하여 혼동할 수 있으므로 주의 할 필요가 있다.

```js
// Rest 파라미터는 인수들의 목록을 배열로 전달 받는다.
function foo(...rest) {
    console.log(rest); // 1, 2, 3 -> [1, 2, 3]
}

// 스프레드 문법은 배열과 같은 이터러블을 펼쳐서 개별적인 값들의 목록을 만든다.
// 1, 2, 3
foo(...[1, 2, 3];
```

## 배열 리터럴 내부에서 사용하는 경우

1. concat

```js
// ES5
var arr = [1, 2].concat([3, 4]);
console.log(arr); // [1, 2, 3, 4]

// ES6
const arr = [...[1, 2], ...[3, 4]];
console.log(arr); // [1, 2, 3, 4];
```

2. splice

```js
// ES5
var arr1 = [1, 4];
var arr2 = [2, 3];

// 세 번째 인수 arr2를 해체하여 전달해야 한다.
// 그렇지 않으면 arr1에 arr2 배열 자체가 추가된다.
arr1.splice(1, 0, arr2);

// 기대값은 [1, 2, 3, 4]지만
console.log(arr1); // [1, [2, 3], 4];

// 다음처럼 코드가 추가되어야 한다.
Array.prototype.splice.apply(arr1, [1, 0].concat(arr2));
console.log(arr1); // [1, 2, 3, 4]
```

- 스프레드 문법 사용

```js
let arr1 = [1, 4];
let arr2 = [2, 3];

arr1.splice(1, 0, ...arr2);
console.log(arr1); // [1, 2, 3, 4];
```

3. 배열 복사

- ES5에선 배열을 복사하려면 slice 메서드를 사용해야 한다.

```js
var origin = [1, 2];
var copy = origin.slice();

console.log(copy); // [1, 2]
console.log(origin === copy); // false
```

- 스프레드 문법 사용

```js
let origin = [1, 2];
let copy = [...origin];

console.log(copy); // [1, 2];
console.log(origin === copy); // false
```

4. 이터러블을 배열로 변환

- ES5에서 이터러블이면서 유사 배열 객체인 arguments를 배열로 반환하려면 slice 메소드 호출

```js
function sum() {
  // 이터러블이면서 유사 배열 객체인 arguments 를 배열로 변환
  var args = Array.prototype.slice.call(arguments);

  return args.reduce(function (pre, cur) {
    return pre + cur;
  }, 0);
}

console.log(sum(1, 2, 3)); // 6
```

- 이터러블뿐만 아니라 이터러블이 아닌 유사 배열 객체도 배열로 변환할 수 있다.

```js
const arrayLike = {
  0: 1,
  1: 2,
  2: 3,
  length: 3,
};

const arr = Array.prototype.slice.call(arrayLike); // [1, 2, 3]
console.log(Array.isArray(arr)); // true
```

- 스프레도 문법을 사용하면 좀 더 간편하게 이터러블을 배열로 변환할 수 있다.

```js
function sum() {
  // 이터러블이면서 유사 배열 객체인 arguments 를 배열로 변환
  return [...arguments].reduce((pre, cur) => pre + cur, 0);
}

console.log(sum(1, 2, 3)); // 6
```

- Rest 파라미터 사용

```js
const sum = (...args) => args.reduce((pre, cur) => pre + cur, 0);

console.log(sum(1, 2, 3));
```

- 단, 이터러블이 아닌 유사 배열 객체는 스프레드 문법의 대상이 될 수 없다.

```js
const arrayLike = {
  0: 1,
  1: 2,
  2: 3,
  length: 3,
};

const arr = [...arrayLike]; // TypeError
```

## 객체 리터럴 내부에서 사용하는 경우

- 스프레드 문법의 대상은 이터러블이어야 하지만 스프레드 프로퍼티 제안은 일반 객체를 대상으로도 스프레드 문법의 사용을 허용한다.

```js
// 스프레드 프로퍼티
// 객체 복사(얕은 복사)
const obj = { x: 1, y: 2 };
const copy = { ...obj };
console.log(copy); // { x: 1, y: 2 }
console.log(obj === copy); // false

// 객체 병합
const merged = { x: 1, y: 2, ...{ a: 3, b: 4 } };
console.log(merged); // { x: 1, y: 2, a: 3, b: 4 }
```

- 스프레드 프로퍼티는 Object.assign 메서드를 대체할 수 있는 간편한 문법이다.

```js
// 객체 병합, 프로퍼티가 중복되는 경우 뒤에 위치한 프로퍼티가 우선권을 갖는다.
const merged = { ...{ x: 1, y: 2 }, ...{ y: 10, z: 3 } };
console.log(merged); // { x: 1, y: 10, z: 3 }

// 특정 프로퍼티 변경
const changed = { ...{ x: 1, y: 2 }, y: 100 };
console.log(changed); // { x: 1, y: 100 }

// 프로퍼티 추가
const added = { ...{ x: 1, Y: 2 }, z: 0 };
console.log(added); // { x: 1, Y: 2, z: 0 }
```

# 디스트럭처링 할당

- 구조 분해 할당은 구조화된 배열과 같은 이터러블 또는 객체를 destructuring 하여 1개 이상의 변수에 개별적으로 할당하는 것을 말한다.
- 배열과 같은 이터러블 또는 객체 리터럴에서 필요한 값만 추출하여 변수에 할당할 때 유용하다.

## 배열 디스트럭처링 할당

- ES6 배열 디스트럭처링 할당의 대상은 이터러블이어야 하며, 할당 기준은 배열의 인덱스다.

```js
const arr = [1, 2, 3];

const [one, two, three] = arr;

console.log(one, two, three); // 1 2 3
```

- 배열 디스트릭처링 할당을 위해서는 할당 연산자 왼쪽에 값을 할당받을 변수를 선언해야한다(이때 배열 리터럴 형태로 선언한다.)

```js
const [x, y] = [1, 2];

// 우변에 이터러블을 할당하지 않으면 에러가 발생한다.
const [x, y]; // SyntaxError

const [a, b] = {}; // TypeError
```

- 배열 디스트럭처링 할당의 변수 선언문은 다음처럼 선언과 할당을 분리할 수도 있다.
- const 키워드는 변수를 선언할 수 없으므로 해당 방식을 권장하지 않는다.

```js
let x, y;
[x, y] = [1, 2];
```

- 배열 디스트럭처링 할당의 기준은 배열의 인덱스다. 즉, 순서대로 할당된다.

```js
const [a, b] = [1, 2];
console.log(a, b); // 1 2

const [c, d] = [1];
console.log(c, d); // 1 undefined

const [e, f] = [1, 2, 3];
console.log(e, f); // 1 2

const [g, , h] = [1, 2, 3];
console.log(g, h); // 1 3
```

- 배열 디스트럭처링 할당을 위한 변수에 기본값을 설정할 수 있다.

```js
// 기본값
const [a, b, c = 3] = [1, 2];
console.log(a, b, c); // 1 2 3

// 기본값보다 할당된 값이 우선한다.
const [e, f = 10, g = 3] = [1, 2];
console.log(e, f, g); //  1 2 3
```

- 배열 디스트럭처링 할당을 위한 변수에 Rest 파라미터와 유사하게 Rest 요소를 사용할 수 있다.

```js
// Rest 요소
const [x, ...y] = [1, 2, 3];
console.log(x, y); // 1 [2, 3]
```

## 객체 디스트럭처링 할당

- ES5에서 객체의 각 프로퍼티를 객체로부터 디스트럭처링하여 변수에 할당하기 위해서는 프로퍼티 키를 사용해야 한다.

```js
var user = {
  firstName: "Sunny",
  lastName: "Son",
};

var firstName = user.firstName;
var lastName = user.lastName;

console.log(firstName, lastName); // Sunny Son
```

- ES6의 객체 디스트럭처링 할당은 객체의 각 프로퍼티틑 객체로부터 추출하여 1개 이상의 변수에 할당한다.
- 객체 디스트럭처링 할당의 대상은 객채이여야 하며, 할당 기준은 프로퍼티 키다.

```js
const user = {
  firstName: "Sunny",
  lastName: "Son",
};

// 프로퍼티 키를 기준으로 디스트럭처링 할당이 이루어진다. 순서는 의미가 없다.
const { lastName, firstName } = user;

console.log(firstName, lastName); // Sunny Son
```

- 객체 디스트럭처링 할당을 위해서는 할당 연산자 왼쪽에 프로퍼티 값을 할당 받을 변수를 선언해야 한다.

```js
const { lastName, firstName } = { firstName: "Sunny", lastName: "Son" };
```

- 우변에 객체 또는 객체로 평가될 수 있는 표현식을 할당하지 않으면 에러가 발생한다.

```js
const {lastName, firstName}; // SyntaxError

const {lastName, firstName} = null; // TypeError
```

- 객체의 프로퍼티 키와 다른 변수 이름으로 프로퍼티 값을 할당받으려면 다음과 같이 변수를 선언한다.

```js
const user = {
  firstName: "Sunny",
  lastName: "Son",
};

// 프로퍼티 키를 기준으로 디스트럭처링 할당이 이루어진다.
// 프로퍼티 키를 변수 선언과 함께 따로 할당한다.
const { lastName: ln, firstName: fn } = user;

console.log(fn, ln); // Sunny Son
```

- 객체 구조분해 할당을 위한 변수에 기본값을 설정할 수 있다.

```js
const { firstName = "Cloud", lastName } = { lastName: "Kim" };
console.log(firstName, lastName); // Cloud Kim

const { lastName: ln, firstName: fn = "Sunny" } = { lastName: "Son" };
console.log(fn, ln); // Sunny Son
```

- 객체 구조분해 할당은 객체에서 프로퍼티 키로 필요한 프로퍼티 값만 추출하여 변수에 할당하고 싶을 때 유용하다.

```js
const str = "Hello";
// String 래퍼 객체로부터 length 프로퍼티만 추출한다.
const { length } = str;
console.log(length); // 5

const todo = { id: 1, content: "HTML", completed: true };
// todo 객체로부터 id 프로퍼티만 추출한다.
const { id } = todo;
console.log(id); // 1
```

- 객체 구조분해 할당은 객체를 인수로 전달받은 함수의 매개변수에도 사용할 수 있다.

```js
function printTodo(todo) {
  console.log(
    `할일 ${todo.content}은 ${todo.completed ? "완료" : "비완료"} 상태입니다.`
  );
}

printTodo({ id: 1, content: "HTML", completed: true });
// 할일 HTML은 완료 상태입니다.
```

- 매개변수 todo에 객체 구조분해 할당을 사용하면 좀 더 간단하고 가독성 좋게 표현할 수 있따.

```js
function printTodo({ content, completed }) {
  console.log(`할일 ${content}은 ${completed ? "완료" : "비완료"} 상태입니다.`);
}

printTodo({ id: 1, content: "HTML", completed: true });
// 할일 HTML은 완료 상태입니다.
```

- 중첩 객체의 경우는 다음과 같이 사용한다.

```js
const user = {
  name: "Sunny",
  address: {
    zipCode: "01023",
    city: "Seoul",
  },
};

// address 프로퍼티 키로 객체를 추출하고 이 객체의 city 프로퍼티 키로 값을 추출한다.
const {
  address: { city },
} = user;
console.log(city); // Seoul
```

- 객체 구조분해 할당을 위한 Rest 파라미터나 Rest 요소와 유사하게 Rest 프포러티를 사용할 수 있다.

```js
const { x, ...rest } = { x: 1, y: 2, z: 3 };
console.log(x, rest); // 1 {y: 2, z: 3}
```

# Set과 Map

## Set

- Set 객체는 중복되지 않는 유일한 값들의 집합이다.

1. Set 객체의 생성

```js
const set = new Set();
console.log(set); // Set(0) {}
```

- Set 생성자 함수는 이터러블을 인수로 전달받아 Set 객체를 생성한다.
- 이때 이터러블의 중복된 값은 Set 객체에 요소로 저장되지 않는다.

```js
const set1 = new Set([1, 2, 3, 3]);
console.log(set1); // Set(3) {1, 2, 3}

const set2 = new Set("hello");
console.log(set2); // Set(4) { 'h', 'e', 'l', 'o' }
```

- 중복을 허용하지 않는 Set 객체의 특성을 활용하여 배열에서 중복된 요소를 제거할 수 있다.

```js
const array = [2, 1, 2, 3, 4, 3, 4];

const uniq1 = (array) => array.filter((v, i, self) => self.indexOf(v) === i);
console.log(uniq1(array)); // [2, 1, 3, 4]

const uniq2 = (array) => [...new Set(array)];
console.log(uniq2(array)); // [2, 1, 3, 4]
```

2. 요소 개수 확인

- Set.prototype.size 프로퍼티를 사용

```js
const { size } = new Set([2, 1, 3, 3]);
console.log(size); // 3
```

- size 프로퍼티는 getter 함수만 존재하는 접근자 프로퍼티
- size 프로퍼티에 숫자를 할당하여 Set 객체의 요소 개수를 변경할 수 없다.

```js
const set = new Set([1, 2, 3]);

set.size = 10; // 무시됨.
console.log(set.size); // 3
```

3. 요소 추가

- Set 객체에 요소를 추가할 때는 Set.prototype.add 메서드를 사용한다.

```js
const set = new Set();
console.log(set); // Set(0) {}

set.add(1);
console.log(set); // Set(1) {1}

// add 메서드는 새로운 요소가 추가된 Set 객체를 반환한다.
// add 메서드를 연속적으로 호출이 가능하다.
set.add(1).add(2);
console.log(set); // Set(2) { 1, 2 }

----
const set = new Set();

// 일치 비교 연산자 ===을 사용하면 NaN과 NaN을 다르다고 평가한다.
// Set 객체는 NaN과 NaN을 같다고 평가하여 중복 추가를 허용하지 않는다.
// +0, -0은 일치 비교 연산자 ===와 마찬가지로 같다고 평가한다.
console.log(NaN === NaN); // false
console.log(+0 === -0); // true

set.add(NaN).add(NaN);
console.log(set); // Set(1) { NaN }

set.add(0).add(-0);
console.log(set); // Set(1) { 0 }
```

4. 요소 존재 여부 확인

- Set.prototype.has 메서드 사용
- has 메서드는 특정 요소의 존재 여부를 나타내는 불리언 값을 반환

```js
const set = new Set([1, 2, 3]);

console.log(set.has(2)); // true
console.log(set.has(4)); // false
```

5. 요소 삭제

- Set.prototype.delete 메서드 사용
- delete 메서드는 삭제 성공 여부를 나타내는 불리언 값을 반환
- delete 메서드는 인덱스가 아니라 삭제하려는 요소값을 인수로 전달해야 한다.

```js
const set = new Set([1, 2, 3]);

// 요소 2 삭제
set.delete(2);
console.log(set); // Set(2) { 1, 3 }

// 존재하지 않는 요소 0을 삭제하면 무시된다.
set.delete(0);
console.log(set); // Set(2) { 1, 3 }
```

6. 요소 일괄 삭제

- Set.prototype.clear 메서드 사용
- clear 메서드는 언제가 undefiend를 반환

```js
const set = new Set([1, 2, 3]);

set.clear();
console.log(set); // Set(0) {}
```

7. 요소 순회

- Set.prototype.forEach 메서드를 사용
- Array의 forEach 메서드처럼 3개의 인수와 함께 콜백함수를 전달
  - 현재 순회중인 요소값
  - 현재 순회중인 요소값
  - 현재 순회 중인 Set 객체 자체

```js
const set = new Set([1, 2, 3]);

set.forEach((v, v2, set) => console.log(v, v2, set));

/*
    1 1 Set(3) { 1, 2, 3 }
    2 2 Set(3) { 1, 2, 3 }
    3 3 Set(3) { 1, 2, 3 }
*/
```

- Set 객체는 이터러블이다.
  - for...of 문
  - 스프레드 문법
  - 배열 구조 분해 할당

```js
const set = new Set([1, 2, 3]);

// for...of 문
for (const value of set) {
  console.log(value); // 1 2 3
}

// 스프레드 문법
console.log([...set]); // [1, 2, 3]

// 배열 디스트럭처링
const [a, ...rest] = set;
console.log(a, rest); // 1 [2, 3]
```

- Set 객체는 요소의 순서에 의미를 갖지 않지만 Set 객체를 순회하는 순서는 요소가 추가된 순서를 따른다.

8. 집합 연산

- Set 객체는 수학적 집합을 구현하기 위한 자료구조
- 집합 연산을 수행하는 프로토타입 메서드를 구현하면 다음과 같다.

- 교집합

```js
Set.prototype.intersection = function (set) {
  const result = new Set();

  for (const value of set) {
    // 2개의 set의 요소가 공통되는 요소이면 교집합의 대상이다.
    if (this.has(value)) {
      result.add(value);
    }
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

console.log(setA.intersection(setB)); // Set(2) { 2, 4 }
console.log(setB.intersection(setA)); // Set(2) { 2, 4 }

// 다음과 같은 방법으로도 가능하다.
Set.prototype.intersection = function (set) {
  return new Set([...this].filter((v) => set.has(v)));
};
```

- 합집합

```js
Set.prototype.usage = function (set) {
  // this(Set 객체 복사)
  const result = new Set(this);

  for (const value of set) {
    // 중복된 요소는 포함되지 않도록 한다.
    result.add(value);
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

console.log(setA.usage(setB)); // Set(4) { 1, 2, 3, 4 }
console.log(setB.usage(setA)); // Set(4) { 2, 4, 1, 3 }

// 다음과 같은 방법으로도 가능하다.
Set.prototype.usage = function (set) {
  return new Set([...this, ...set]);
};
```

- 차집합

```js
Set.prototype.difference = function (set) {
  // this(Set 객체 복사)
  const result = new Set(this);

  for (const value of set) {
    // 어느 한쪽 집합에는 존재하지만 다른 한쪽 집합에는 존재하지 않는 요소로 구성한 집합이다.
    result.delete(value);
  }

  return result;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

console.log(setA.difference(setB));
console.log(setB.difference(setA));

// 다음과 같은 방법으로도 가능하다.
Set.prototype.difference = function (set) {
  return new Set([...this].filter((v) => !set.has(v)));
};
```

- 부분집합과 상위 집합

```js
Set.prototype.isSuperset = function (subset) {
  for (const value of subset) {
    // superset의 모든 요소가 subset의 모든 요소를 포함하는지 확인
    if (!this.has(value)) {
      return false;
    }
  }
  return true;
};

const setA = new Set([1, 2, 3, 4]);
const setB = new Set([2, 4]);

console.log(setA.isSuperset(setB));
console.log(setB.isSuperset(setA));

// 다음과 같은 방법으로도 가능하다.
Set.prototype.isSuperset = function (subset) {
  const supersetArr = [...this];
  return [...subset].every((v) => supersetArr.includes(v));
};
```

## Map

- Map 객체는 키와 쌍으로 이루어진 컬렉션이다. Map 객체는 객체와 유사하나 다음과 같은 차이가 있다.
  - 키로 사용할 수 있는 값: 객체(문자열 또는 심벌 값), Map 객체(객체를 포함한 모든 값)
  - 이터러블: 객체(X), Map 객체(O)
  - 요소 개수 확인: Object.keys(obj).length, Map 객체(map.size)

1. Map 객체의 생성

- Map 객체는 Map 생성자 함수로 생성한다. Map 생성자 함수에 인수를 전달하지 않으면 빈 Map이 생성된다.

```js
const map = new Map();
console.log(map); // Map(0) {}
```

- Map 생성자 함수는 이터러블을 인수로 전달받아 Map 객체를 생성한다.
- 인수로 전달되는 이터러블은 키와 쌍으로 이루어진 요소로 구성되어야 한다.

```js
const map1 = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);
console.log(map1); // Map(2) { 'key1' => 'value1', 'key2' => 'value2' }

const map2 = new Map([1, 2]); // TypeError
```

- 중복된 키는 Map 객체에 요소로 저장되지 않는다.

```js
const map = new Map([
  ["key1", "value1"],
  ["key1", "value2"],
]);
console.log(map); // Map(1) { 'key1' => 'value2'}
```

2. 요소 개수 확인

- Map.prototype.size 프로퍼티 사용

```js
const { size } = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);
console.log(size); // 2
```

- size 프로퍼티는 getter 함수만 존재하는 접근자 프로퍼티
- size 프로퍼티에 숫자를 할당하여 Set 객체의 요소 개수를 변경할 수 없다.

```js
const map = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);

map.size = 10;
console.log(map.size); // 2
```

3. 요소 추가

- Map.prototype.set 프로퍼티를 사용

```js
const map = new Map();
console.log(map); // Map(0) {}

map.set("key1", "value1");
console.log(map); // Map(1) { 'key1' => 'value1' }

// set 메서드는 새로운 요소가 추가된 Map 객체를 반환한다.
// set 메서드는 연속적으로 호출할 수 있다.
map.set("key1", "value1").set("key2", "value2");
console.log(map); // Map(2) { 'key1' => 'value1', 'key2' => 'value2' }

// Map 객체에 중복된 키를 갖는 요소의 추가는 허용되지 않는다.
// 에러는 발생하지 않고 무시한다.
map.set("key1", "value1").set("key1", "value2");
console.log(map); // Map(1) { 'key1' => 'value2' }

// NaN, +0,, -0도 Set과 마찬가지로 같다고 평가하여 중복 추가를 허용하지 않는다.
map.set(NaN, "value1").set(NaN, "value2");
console.log(map); // Map(1) { NaN => 'value2' }

map.set(0, "value1").set(-0, "value2");
console.log(map); // Map(1) { 0 => 'value2' }
```

- 객체는 문자열 또는 심벌 값만 키로 사용할 수 있다.
- Map 객체는 키 타입에 제한이 없다.(객체를 포함한 모든 값을 키로 사용할 수 있다)

```js
const map = new Map();

const lee = { name: "Lee" };
const son = { name: "Son" };

map.set(lee, "developer").set(son, "designer");
console.log(map);

/*
    Map(2) 
    {
      { name: 'Lee' } => 'developer',
      { name: 'Son' } => 'designer'
    }
*/
```

4. 요소 취득

- Map.prototype.get 메서드 사용
- get 메서드의 인수로 키를 전달하면 Map 객체에서 인수로 전달한 키를 갖는 값을 반환
- 존재하지 않으면 undefined를 반환

```js
const map = new Map();

const lee = { name: "Lee" };
const son = { name: "Son" };

map.set(lee, "developer").set(son, "designer");

console.log(map.get(lee)); // developer
console.log(map.get("key")); // undefined
```

5. 요소 존재 여부 확인

- Map.prototype.has 메서드를 사용
- has 메서드는 특정 요소의 존재 여부를 나타내는 불리언 값을 반환

```js
const map = new Map();

const lee = { name: "Lee" };
const son = { name: "Son" };

map.set(lee, "developer").set(son, "designer");

console.log(map.has(lee)); // true
console.log(map.has("key")); // false
```

6. 요소 삭제

- Map.prototype.delete 메서드를 사용
- delete 메서드는 삭제 성공 여부를 나타내는 불리언 값을 반환

```js
const map = new Map();

const lee = { name: "Lee" };
const son = { name: "Son" };

map.set(lee, "developer").set(son, "designer");

map.delete(lee);
console.log(map); // Map(1) { { name: 'Son' } => 'designer' }

// 만약 존재하지 않는 키로 삭제하려하면 에러 없이 무시된다.
const map = new Map();

const lee = { name: "Lee" };
const son = { name: "Son" };

map.set(lee, "developer").set(son, "designer");

map.delete("key2");
console.log(map);

/*
    Map(2) 
    {
      { name: 'Lee' } => 'developer',
      { name: 'Son' } => 'designer'
    }
*/

// delete 메서드는 연속적으로 호출할 수 없다.
map.delete(lee).delete(son); // TypeError
```

7. 요소 일괄 삭제

- Map.prototype.clear 메서드 사용
- clear 메서드는 언제나 undefined를 반환

```js
const map = new Map();

const lee = { name: "Lee" };
const son = { name: "Son" };

map.set(lee, "developer").set(son, "designer");

map.clear();
console.log(map); // Map(0) {}
```

8. 요소 순회

- Map 객체는 이터러블이다.
  - for...of 문
  - 스프레드 문법
  - 배열 구조 분해 할당

```js
const map = new Map();

const lee = { name: "Lee" };
const son = { name: "Son" };

map.set(lee, "developer").set(son, "designer");

// for...of 문
for (const entry of map) {
  console.log(entry);
  // [ { name: 'Lee' }, 'developer' ]
  // [ { name: 'Son' }, 'designer' ]
}

// 스프레드 문법
console.log([...map]);
// [ [ { name: 'Lee' }, 'developer' ], [ { name: 'Son' }, 'designer' ] ]

// 배열 디스트럭처링
const [a, b] = map;
console.log(a, b);
// [ { name: 'Lee' }, 'developer' ] [ { name: 'Son' }, 'designer' ]
```

- Map 객체는 이터러블이면서 동시에 이터레이터인 객체를 반환하는 메서들 제공
  - Map.prototype.keys: Map 객체에서 요소키를 값으로 갖는 이터러블 / 이터레이터인 객체를 반환한다.
  - Map.prototype.values: Map 객체에서 요소값을 값으로 갖는 이터러블 / 이터레이터인 객체를 반환한다.
  - Map.prototype.entries: Map 객체에서 요소키와 요소값을 값으로 갖는 이터러블 / 이터레이터인 객체를 반환한다.
- Map 객체는 요소의 순서에 의미를 갖지 않지만 Map 객체를 순회하는 순서는 요소가 추가된 순서를 따른다.

# 브라우저의 렌더링 과정

- 웹 애플리케이션의 클라이어늩 사이드 자바스크립트는 브라우저에서 HTML, CSS와 함께 실행

  - 브라우저 환경을 고려할 때 더 효율적인 클라이언트 사이드 자바스크립트 프로그래밍이 가능하다.

- 브라우저는 다음과 같은 과정을 거쳐 렌더링을 수행한다.
  1. 브라우저는 HTML, CSS, 자바스크립트, 이미지, 폰트 파일 등 렌더링에 필요한 리소를 요청하고 서버로부터 응답을 받느다.
  2. 브라우저의 렌더링 엔진은 서버로부터 응답된 HTML과 CSS를 파싱하여 DOM과 CSSOM을 생성하고 이들을 결합하여 렌더 트리를 생성한다.
  3. 브라우저의 자바스크립트 엔진은 서버로부터 응답된 자바스크립트를 파싱하여 AST를 생성하고 바이트코드로 변환하여 실행한다.
  4. 렌더 트리를 기반으로 HTML 요소의 레이아웃을 계산하고 브라우저 화면에 HTML 요소에 페인팅한다.

## 요청과 응답

- 브라우저의 핵심 기능은 필요한 리소스를 서버에 요청하고 서버로부터 응답받아 브라우저에 시각적으로 렌더링하는 것
  - 렌더링에 필요한 리소스는 모두 서버에 존재하므로 필요한 리소스를 서버에 요청하고 서버가 응답한 리소를 파싱하여 렌더링하는 것

## HTTP 1.1과 HTTP 2.0

- HTTP는 웹에서 브라우저와 서버가 통신하기 위한 프로토콜
- HTTP/1.1은 기본적으로 커넥션당 하나의 요청과 응답만 처리
  - 여러개의 요청을 한 번에 전송할 수 없고 응답 또한 마찬가지
  - HTML 문서 내에 포함된 여러개의 리소스 요청이 개별적으로 전송되고 응답 또한 개별적으로 전송된다.
- HTTP/1.1은 리소스의 동시 전송이 불가능한 구조이므로 요청할 리소스의 개수에 비례하여 응답 시간도 증가하는 단점이 있다.
- HTTP/2는 커넥션당 여러개의 요청과 응답, 즉 다중 요청/응답이 가능하다.
  - 동시 전송이 가능하므로 HTTP/1.1에 비해 페이지 로드 속도가 약 50% 정도 빠르다.

## HTML 파싱과 DOM 생성

- 브라우저의 요청에 서버가 응답한 HTML 문서는 문자열로 이루어진 순수한 텍스트
- HTML 문서를 브라우저가 이해할 수 있는 자료구조(객체)로 변환하여 메모리에 저장해야 렌더링이 가능하다.
- 브라우저의 렌더링 엔진은 HTML 문서를 파싱하여 브라우저가 이해할 수 있는 자료구조인 DOM을 생성한다.
  1. 서버가 존재하는 HTML 파일이 브라우저의 요청에 의해 응답된다.
  2. 브라우저는 서버가 응답한 HTML 문서를 바이트 형태로 응답받는다.
  3. 문자열로 변환된 HTML 문서를 읽어 들여 문법적 의미를 갖는 코드의 최소 단위인 토큰(token)을 분해한다.
  4. 각 토콘들을 객체로 변환하여 노드들을 생성한다.
  5. HTML 문서는 HTML 요소들의 집합으로 이루어지며 HTML 요소는 중첩 관계를 갖는다.
- DOM은 HTML 문서를 파싱한 결과물

## CSS 파싱과 CSSOM 생성

## 렌더 트리 생성

## 자바스크립트 파싱과 실행

## 리플로우와 리페인트

## 자바스크립트 파싱에 의함 HTML 파싱 중단

## script 태그의 async.defer 어트리뷰트
