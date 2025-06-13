기본적인 거 말고 모를만한 거 정리
## string

문자열은 불변이다. 변경 시 새 문자열이 만들어진다. (Java와 동일)

## number

정수, 실수, NaN, Infinity 등 모든 숫자를 표현한다.

JavaScript의 모든 숫자는 64비트 부동소수점(IEEE 754)

- 64비트 부동 소수점
	- 2^53(약 9,007,199,254,740,992)까지는 정확한 정수 표현 가능

- NaN
	- 숫자 타입이지만 유효한 숫자가 아닌 특별한 값
	- 숫자 연산에서 오류가 발생했을 때 결과로 나온다.
	- NaN === Nan 은 false
	- NaN 확인 시 `isNaN()` 사용해야 한다. (`Number.isNaN()` 이 더 엄격하고 권장됨) 
- Infinity
	- 무한대 개념을 표현한다.
	- IEEE 754 64비트 부동소수점 표준에 정의된 값 중 하나
	- Infinity, -Infinity
	- 매우 큰 수, 0으로 나눌 경우 무한대 발생

## boolean

기존 boolean과 동일

개발 하다보면 `!!` 연산자를 볼 수 있는데, 이는 값을 boolean 타입으로 변환할 때 자주 사용된다.

```ts
const num0:number = 0;
const num1:number = 1;

if(0) // false
if(1) // true

```

보통 0, 1은 숫자이지만 0은 false 이외에는 true 인 걸 알 수 있다.
그렇다고 `boolean flag = 0` 이 true는 아니기 때문에 형변환을 해줘야 되는데, `!num0` 을 할 경우 자동 형변환이 되어 true를 반환하게 된다.
그래서 기존의 num0의 boolean 값을 알고 싶을 때 `!!` 를 사용하면 false를 반환한다.

```ts
const num0:number = 0;

const flag:boolean = !!num0;

console.log(flag) // false
```

## null

"값이 없음"

ts에서는 null 타입이 존재한다.

```ts
let nothing: null = null;
```

TypeScript의 `strictNullChecks` 옵션이 켜져 있을 경우, null은 무조건 null 타입에만 할당할 수 있다.

## undefined

"값이 할당되지 않음"

```ts
let notAssigned: undefined = undefined; let value: number | undefined;
```

1. 변수를 선언만 하고 값을 할당하지 않으면 기본값이 `undefined`
2. 필드가 아예 없는 경우에 그 필드에 접근하면 나오는 값

### null과 undefined 차이

- API 응답에서 필드가 아예 없으면 → `undefined` (또는 프로퍼티가 없음)
- API 응답에서 필드는 있지만 값이 없으면 → `null`

```ts
interface User {
  name: string;
  age: number | null;       // 값이 없을 수 있으니 null 허용
  age2: number | undefined; // 필드가 아예 없거나, 값이 할당 되지 않음
  email?: string;           // 필드가 없을 수도 있으니 optional
}
```

## symbol

고유하고 변경 불가능한 값(주로 객체의 프로퍼티 키로 사용)

```ts
let id: symbol = Symbol("id");
let anotherId: symbol = Symbol("id");
console.log(id === anotherId); // false (항상 고유)
```

심볼은 객체의 숨겨진 프로퍼티 키로 주로 사용한다.

```ts
const id = Symbol('id');  // 심볼 생성

const user = {
  [id]: 12345,            // 심볼을 키로 사용
  name: 'Joo'
};

console.log(user.name);   // 'Joo'
console.log(user[id]);    // 12345

// 일반적인 방법으로는 심볼 키 프로퍼티가 보이지 않음
console.log(Object.keys(user)); // ['name']
console.log(Object.getOwnPropertySymbols(user)); // [Symbol(id)]
```

## bigint

아주 큰 수

number 와 bigint 는 연산할 수 없다.

## any

모든 타입 혀용

## unknown

타입을 알 수 없을 때 사용한다. any와 달리 실제로 사용하려면 타입 검사가 필요하다.

```ts
let value: unknown = "hello";
// value.toUpperCase(); // 오류!
if (typeof value === "string") {
  value.toUpperCase(); // OK
}
```

### 언제 사용하는가

1. 외부에서 들어오는 값의 타입이 불명확할 때. 
2. any는 모든 타입을 허용하지만, 타입 검사를 완전히 무시해서 런타임 오류 가능성이 커진다. 반면 unknown은 어떤 값이든 할당할 수 있지만, 실제 사용하려면 타입 검사는 반드시 해야 해서 안전하다.
3. 타입 좁히기(Type Narrowing)를 강제할 때. unknown 타입 변수는 바로 사용할 수 없고, 타입을 확인하거나 단언(assertion) 해야만 사용 가능하다. 코드가 더 안전해지고, 예상치 못한 타입 오류를 줄일 수 있다.

## void

함수가 값을 반환하지 않을 때 사용

## never

절대 발생하지 않는 값 (예: 함수가 예외를 던지거나 무한 루프일 때)

```ts
function error(message: string): never {
  throw new Error(message);
}
```

반환값이 절대 존재하지 않음을 명시할 때 사용한다.

