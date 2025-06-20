## 조건부 타입 (Conditional Types)

- 타입을 조건문처럼 분기해서 새로운 타입을 만든다.
- 문법 `T extends U ? X : Y`

```ts
type IsString<T> = T extends string ? true : false;

type A = IsString<string>; // true
type B = IsString<number>; // false
```

## Mapped Types

- 기존 타입의 프로퍼티를 변형해서 새로운 타입을 만든다.
- 주로 `keyof` 와 함께 사용

```ts
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

type User = { name: string; age: number };
type ReadonlyUser = Readonly<User>;
// { readonly name: string; readonly age: number }
```

## 키워드 타입 연산자

- `keyof` : 타입의 키(프로퍼티 이름)들을 유니온 타입으로 추출

```ts
type UserKeys = keyof User; // "name" | "age"
```

- `typeof` : 값의 타입을 추론

```ts
const user = { name: "joo", age: 30 };
type UserType = typeof user; // { name: string; age: number }
```

- `infer` : 조건부 타입 내에서 타입을 추론할 때 사용

```ts
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;

type Fn = (x: number) => string;
type Result = ReturnType<Fn>; // string
```

