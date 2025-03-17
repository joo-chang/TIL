> 제네릭은 코드의 재사용성과 타입 안정성을 동시에 확보할 수 있도록 도와주는 기능이다. 즉, 구체적인 타입을 미리 정하지 않고, 필요할 때마다 유연하게 타입을 지정할 수 있게 해준다.

## 기본 개념

함수를 작성할 때 특정 타입에 의존하지 않고, 여러 타입에 대해 동작하도록 만들고 싶을 때 제네릭을 사용한다. 

예를 들어, 입력받은 값을 그대로 반환하는 함수

```ts
function identity<T>(arg: T): T {
	return arg;
}

const result1 = identity<string>("Hello"); // string type
const result2 = identity<number>(123); // number type

```

여기서  `<T>` 는 타입 변수로, 함수를 호출할 때 실제 타입으로 대체된다. 이를 통해 함수가 어떤 타입을 받아도 그에 맞는 타입을 반환할 수 있게 된다.
<br>

## 제네릭 활용 이유

- 재사용성 증가 : 동일한 로직을 다양한 데이터 타입에 대해 사용할 수 있다.
- 타입 안정성 보장 : 코드 작성 시점에 타입을 체크해 오류를 줄일 수 있으며, 런타임 이전에 문제를 발견할 수 있다.
- 유연한 설계 : 구체적인 타입에 얽매이지 않고, 필요한 시점에 타입을 결정할 수 있어 모듈성과 확장성이 뛰어나다.
<br>

## 제네릭 활용 예시

### 제네릭 인터페이스

```ts
interface ApiResponse<T> {
	data: T;
	status: number;
	message: string;
}

const userReesponse: ApiResponse<{ name: string; age: number }> = {
	data: { name: "joo", age: 29},
	status: 200,
	message: "성공"
};
```

여기서 T는 사용 시점에 구체적인 타입(객체)을 지정하게 된다.

### 제네릭 클래스

클래스에서 제네릭을 사용하면 여러 타입을 처리하는 범용 클래스를 만들 수 있다.

```ts
class Container<T> {
	private _value: T;

	constructor(value: T) {
		this._value = value;
	}

	get value(): T {
		return this._value;
	}

	set value(newValue: T) {
		this._value = newValue;
	}
}

const stringContainer = new Container<string>("문자열");
console.log(stringContainer.value); // "문자열"
```

### 제네릭 제약조건 (Constraints)

모든 타입이 아닌, 특정 조건을 만족하는 타입만 받도록 제한할 수 있다.

```ts
interface Lengthwise {
	length: number;
}

function logLength<T extends Lengthwise>(item: T): void {
	console.log(item.length);
}

logLength("Hello");     // 문자열은 length 속성을 가짐
logLength([1, 2, 3]);   // 배열도 length 속성이 있음
// logLength(42);       // 숫자는 length 속성이 없으므로 오류 발생
```

위에서는 제네릭 타입 T가 Lengthwise 인터페이스를 반드시 만족하도록 제한했다.

### 여러 개의 제네릭 사용

함수나 클래스에 두 개 이상의 제네릭 타입 변수를 사용하여 복합적인 타입 관계를 정의할 수 있다.

```ts
function swap<K, V>(key: K, value: V): [V, K] {
	return [value, key];
}
const swapped = swap("age", 30); // [30, "age"]
```

두 개의 타입 매개변수를 사용해 입력 순서를 바꾸거나, 타입 간의 관계를 명확하게 표현할 수 있다.
<br>

## 결론

제네릭은 함수, 인터페이스, 클래스 등 다양한 구조에서 사용 가능하며, 코드의 재사용성과 유지보수성을 크게 향상시킨다.

1. 구체적인 타입을 나중에 지정할 수 있어 유연한 코드 설계가 가능하다.
2. 타입 안전성을 보장하여 컴파일 시점에서 오류를 사전에 예방할 수 있다.

