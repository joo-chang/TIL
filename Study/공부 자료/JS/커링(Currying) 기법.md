> 커링(Currying)은 함수형 프로그래밍의 핵심 기법 중 하나로, 여러 개의 인자를 가진 함수를 인자 하나만 받는 함수들의 체인으로 변환하는 과정이다. 이 기법은 함수의 재사용성과 유연성을 높이는 데 큰 도움을 준다.

## 기본 개념

커링은 n개의 인자를 받는 함수를 각각 하나의 인자만 받는 n개의 함수로 분리하는 기법이다. 각 단계에서 인자 하나를 받아 처리하고, 다음 인자를 기다리는 새로운 함수를 반환한다.

예를 들어, 세 개의 인자를 받아 더하는 함수를 커링으로 변환해보자:

```js
// 일반적인 함수
function add(a, b, c) {
  return a + b + c;
}

// 커링된 함수
function curriedAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

// 사용 예시
const result1 = add(1, 2, 3); // 6
const result2 = curriedAdd(1)(2)(3); // 6
```

화살표 함수를 사용하면 더 간결하게 표현할 수 있다:

```js
const curriedAdd = a => b => c => a + b + c;
```

## 커링 활용 이유

- **부분 적용(Partial Application)**: 일부 인자만 미리 적용하여 재사용 가능한 함수를 만들 수 있다.
- **함수 조합성 향상**: 작은 단위의 함수를 조합하여 복잡한, 고차원 함수를 구성할 수 있다.
- **지연 실행**: 모든 인자가 제공될 때까지 함수 실행을 지연시킬 수 있다.
- **코드 가독성 향상**: 복잡한 로직을 작은 단위로 분리하여 더 명확하고 읽기 쉬운 코드를 작성할 수 있다.

## 커링 활용 예시

### 범용 커링 함수 만들기

일반 함수를 커링 함수로 변환하는 유틸리티 함수를 구현할 수 있다:

```js
function curry(fn) {
  return function curried(...args) {
    // 1. 충분한 인자가 전달되었는지 확인
    if (args.length >= fn.length) {
      // 2. 충분한 인자가 있으면 원래 함수 실행
      return fn.apply(this, args);
    } else {
      // 3. 인자가 부족하면 나머지 인자를 기다리는 새 함수 반환
      return function(...nextArgs) {
        // 4. 이전 인자와 새 인자를 합쳐서 다시 curried 함수 호출
        return curried.apply(this, args.concat(nextArgs));
      };
    }
  };
}
// 사용 예시
function sum(a, b, c) {
  return a + b + c;
}

const curriedSum = curry(sum);
console.log(curriedSum(1)(2)(3)); // 6
console.log(curriedSum(1, 2)(3)); // 6
console.log(curriedSum(1)(2, 3)); // 6
```

### 부분 적용을 통한 함수 재사용

커링을 이용해 특정 인자를 고정한 새로운 함수를 만들 수 있다:

```js
const multiply = a => b => a * b;

// 2를 곱하는 함수
const double = multiply(2);
// 3을 곱하는 함수
const triple = multiply(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```

### 이벤트 핸들러 최적화

커링은 이벤트 핸들러에서도 유용하게 활용될 수 있다:

```js
const handleEvent = eventType => element => callback => {
  element.addEventListener(eventType, callback);
  return () => element.removeEventListener(eventType, callback);
};

// 클릭 이벤트 전용 핸들러
const handleClick = handleEvent('click');

// 특정 버튼에 대한 핸들러
const buttonClickHandler = handleClick(document.getElementById('myButton'));

// 이벤트 등록
const removeListener = buttonClickHandler(e => console.log('Button clicked!'));

// 나중에 필요하면 이벤트 제거
// removeListener();
```

### 함수형 프로그래밍 유틸리티

배열 메서드와 함께 사용하면 더 강력한 기능을 구현할 수 있다:

```js
const map = fn => array => array.map(fn);
const filter = predicate => array => array.filter(predicate);
const reduce = (fn, initial) => array => array.reduce(fn, initial);

// 숫자 배열의 각 요소를 제곱하는 함수
const square = x => x * x;
const squareAll = map(square);

// 짝수만 필터링하는 함수
const isEven = x => x % 2 === 0;
const filterEven = filter(isEven);

// 함수 조합
const numbers = [1, 2, 3, 4, 5];
const result = filterEven(squareAll(numbers)); // [4, 16]
```

## 결론

커링은 JavaScript에서 함수형 프로그래밍을 구현하는 강력한 도구다.

1. 함수의 재사용성을 높이고 코드의 중복을 줄여준다.
2. 함수 조합을 통해 복잡한 로직을 더 간결하고 명확하게 표현할 수 있다.
3. 함수의 실행을 지연시켜 필요한 시점에 필요한 인자만으로 함수를 호출할 수 있다.
4. 부분 적용을 통해 특정 상황에 맞는 특화된 함수를 쉽게 생성할 수 있다.

단, 과도한 커링은 코드의 복잡성을 증가시킬 수 있으므로, 적절한 상황에서 균형 있게 사용하는 것이 중요하다.