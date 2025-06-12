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

변수를 선언만 하고 값을 할당하지 않으면 기본값이 `undefined`

### null과 undefined 차이

- API 응답에서 필드가 아예 없으면 → `undefined` (또는 프로퍼티가 없음)
- API 응답에서 필드는 있지만 값이 없으면 → `null`

```ts
interface User {
  name: string;
  age: number | null;       // 값이 없을 수 있으니 null 허용
  email?: string;           // 필드가 없을 수도 있으니 optional
}
```

## symbol

## bigint

## any

## unknown

## void

## never