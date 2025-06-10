> 함수와 그 함수가 선언될 때의 렉시컬 환경(스코프)을 함께 기억하는 객체

쉽게 말해, 내부 함수가 외부 함수의 변수에 접근할 수 있게 해주는 매커니즘이다.

## 동작 원리

JS에서 함수가 선언될 때, 그 함수는 자신이 선언된 위치의 스코프(환경)를 기억한다.
외부 함수가 실행을 끝내서 스코프가 사라진 것 같아도, 내부 함수가 참조하고 있다면 그 변수들은 계속 살아있게 된다. 

이러한 현상을 클로저라고 한다.

## 예시 코드

```js
function makeCounter() {
  let count = 0; // 외부 변수
  return function () { // 내부 함수
    count++;
    return count;
  }
}

const counter = makeCounter();
console.log(counter()); // 1
console.log(counter()); // 2
```

- makeCounter 함수가 실행되면 내부에 count 변수가 만들어진다.
- return function() {} 이 부분이 클로저.
- 반환된 함수는 count에 계속 접근 가능하다.
- makeCounter의 실행이 끝나도, 반환된 함수가 count를 기억하고 있기 때문에 값이 계속 누적된다.

## 특징

- 데이터 은닉 및 캡슐화 : 바깥에서 직접 변수에 접근하지 못하고, 함수로만 접근 가능하게 만들어 정보 은닉 가능
- 상태 유지 : 함수 실행이 끝난 뒤에도 특정 상태를 유지해야 할 때 유용
- 콜백, 이벤트, 비동기 등 : 비동기 코드에서도 자신만의 스코프를 유지할 수 있게 해준다.

### 데이터 은닉 및 캡슐화 예시

```js
function createCounter() {
  let count = 0;
  return {
    increase: () => ++count,
    decrease: () => --count,
    get: () => count
  };
}

const counter = createCounter();
console.log(counter.increase()); // 1
console.log(counter.get());      // 1
console.log(counter.decrease()); // 0
```

### 콜백 함수 클로저 예시

```js
function makeAdder(x: number) {
  return function(y: number) {
    return x + y;
  };
}

const add = makeAdder(5);
console.log(add(3)); // 8
```

add는 `x=5` 를 기억하고, 나중에 `y` 를 받아 더해준다.

## 자주 사용되는 예시

- setTimeout, setInterval 콜백
- 이벤트 핸들러
- 함수형 프로그래밍 패턴(커링 등)


## 추가 설명

### 렉시컬 스코프 (lexical scope)

> 렉시컬 스코프는 프로그래밍 언어에서 변수의 유효 범위(scope)를 결정하는 방식 중 하나

쉽게 말해, 변수가 어디서 선언되었느냐에 따라 그 변수를 어디서 참조할 수 있는지가 결정되는 것을 의미한다.

- 함수나 블록이 정의된 위치(코드 상의 위치)를 기준으로 변수의 접근 범위가 정해진다.
- 함수 내부에서 변수를 찾을 때, 먼저 그 함수 내부에서 변수를 찾고, 없으면 함수가 정의된 외부 환경(상위 스코프)에서 변수를 찾는 식으로 위로 올라가면서 찾게 된다.

```js
let x = 10;

function foo() {
  console.log(x);  // 여기서 x는 10을 참조
}

function bar() {
  let x = 20;
  foo();  // foo가 선언된 위치의 x(10)를 참조. bar의 x(20)가 아님
}

bar();  // 출력: 10

```

