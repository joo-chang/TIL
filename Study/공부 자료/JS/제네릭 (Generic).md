> 제네릭은 타입을 함수의 매개변수처럼 다루는 것이다.
> 함수, 인터페잇, 클래스 등에서 타입을 외부에서 받아서 사용하는 방식이다.
> 재사용 가능한 컴포넌트를 만들 때 타입을 유연하게 지정할 수 있다.

## 기본 문법

```ts
function identity<T>(arg: T): T {
  return arg;
}

const str = identity<string>("hello"); // T = string
const num = identity<number>(123);     // T = number

```

1.  `<T>` 는 타입 매개변수를 선언하는 부분
2. `arg: T` 는 매개변수 타입, `: T` 는 반환 타입
3. 호출 시 타입을 명시하거나, TS가 자동 추론

## 제네릭 인터페이스와 타입 별칭

```ts
interface Box<T> {
  value: T;
}

const box1: Box<string> = { value: "hello" };
const box2: Box<number> = { value: 123 };

```

## 제네릭 클래스

```ts
class Stack<T> {
  private items: T[] = [];

  push(item: T) {
    this.items.push(item);
  }

  pop(): T | undefined {
    return this.items.pop();
  }
}

const stack = new Stack<number>();
stack.push(10);
console.log(stack.pop()); // 10

```

## 제네릭 제약조건 (Constraints)

- 타입 매개변수에 조건을 걸어 특정 타입만 받도록 제한 가능

```ts
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}

logLength("hello");       // OK (string has length)
logLength([1, 2, 3]);     // OK (array has length)
// logLength(123);        // 오류! number에는 length 없음

```

위에서는 number, string 타입이 length라는 속성을 가지고 있으므로 정상 동작 하는 것

## 여러 타입 매개변수

```ts
function merge<T, U>(obj1: T, obj2: U): T & U {
  return { ...obj1, ...obj2 };
}

const merged = merge({ name: "joo" }, { age: 30 });
console.log(merged.name); // "joo"
console.log(merged.age);  // 30

```

## 기본 타입 매개변수

```ts
function createArray<T = string>(length: number, value: T): T[] {
  return new Array(length).fill(value);
}

const arr = createArray(3, "hello"); // T는 string으로 기본 설정

```

- `<T = string>` T에 기본 타입을 string으로 지정
- 즉 createArray를 호출할 때 타입을 명시하지 않으면 T가 자동으로 string이 된다.

## 실전 예제 : API 응답

```ts
interface ApiResponse<T> {
  data: T;
  error?: string;
}

function fetchData<T>(url: string): Promise<ApiResponse<T>> {
  // fetch 구현 생략
  return fetch(url).then(res => res.json());
}

interface User {
  id: number;
  name: string;
}

fetchData<User>("/api/user/1").then(response => {
  console.log(response.data.name);
});

```

