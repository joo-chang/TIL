# 고급 타입(Mapped, Conditional, Template Literal Types 등)

## 서론(개요)
타입스크립트의 고급 타입은 복잡한 데이터 구조와 다양한 상황에 유연하게 대응하기 위해 도입된 강력한 기능이다. 단순한 인터페이스나 타입 선언을 넘어서, 동적 타입 생성·가공, 조건부 타입 적용, 문자열 기반 타입 생성 등을 통해 코드의 재사용성과 안정성을 극대화할 수 있다. 실무에서는 유지보수성과 타입 안전성을 높이기 위해 고급 타입의 활용 빈도가 꾸준히 증가하는 추세이다.

## 주요 개념

- **Mapped Types(맵드 타입)**  
  기존 타입의 모든 속성을 반복적으로 순회하여, 각 속성에 특정 변환을 적용해 새로운 타입을 생성한다. 대표적으로 `Partial<T>`, `Readonly<T>`, `Record<K, T>` 등이 있다.

- **Conditional Types(조건부 타입)**  
  타입 관계(extends 여부 등)에 따라 타입을 동적으로 분기한다. 예: `T extends U ? X : Y` 형태로 사용하며, API 응답 분기, 공통 유틸리티 제작 등에서 유용하게 쓰인다.

- **Template Literal Types(템플릿 리터럴 타입)**  
  문자열 리터럴과 유사하게, 타입도 백틱(`)과 `${}` 구문을 활용하여 여러 리터럴 타입을 조합하거나 파생 타입을 생성한다.

## 실습 예제 및 심화 활용

### 1. Mapped Type 활용 예제

```ts
// 기존 타입 선언
type User = {
  id: number;
  name: string;
  email: string;
};

// 모든 속성을 Optional로 변환 (Partial 동작의 원리)
type OptionalUser = {
  [K in keyof User]?: User[K];
};

// 모든 속성을 Readonly로 변환 (Readonly 동작의 원리)
type ReadonlyUser = {
  readonly [K in keyof User]: User[K];
};
```

### 2. Conditional Type 활용 및 고급 패턴

```ts
// 조건부 타입 기본 예시
type IsString<T> = T extends string ? "문자열" : "문자열 아님";

type A = IsString<string>; // "문자열"
type B = IsString<number>; // "문자열 아님"

// 조건부 타입을 활용한 API 응답 타입 처리
type ApiResponse<T> = T extends Error ? { error: true; msg: string }
                                     : { error: false; data: T };

// 예시 사용
type Ok = ApiResponse<{ name: string }>; // { error: false; data: { name: string }}
type Fail = ApiResponse<Error>; // { error: true; msg: string }
```

### 3. Template Literal Types 심화 예제

```ts
type Color = "red" | "blue";
type Shade = "light" | "dark";
type ColorVariant = `${Shade}-${Color}`; // "light-red" | "light-blue" | "dark-red" | "dark-blue"

// HTML input name key 자동 생성
type InputField<T> = `input-${T}`;

type UserKeys = keyof User; // "id" | "name" | "email"
type InputUserField = InputField<UserKeys>; // "input-id" | "input-name" | "input-email"
```

### 4. 고급 확장: 커스텀 Mapped & Conditional Type 제작

```ts
// 모든 속성을 Promise로 감싼 타입 (실무에서 비동기 데이터 모델링 시 활용)
type Promisify<T> = {
  [K in keyof T]: Promise<T[K]>;
};

type PromisedUser = Promisify<User>;
/*
{
  id: Promise<number>;
  name: Promise<string>;
  email: Promise<string>;
}
*/
```

## 정리/요약

고급 타입은 타입 재사용성과 확장성을 뛰어나게 만들어 준다. Mapped Types는 반복적 변환, Conditional Types는 동적 타입 분기, Template Literal Types는 새로운 리터럴 타입의 조합을 가능하게 하여, 복잡한 모델링이나 대규모 프로젝트 구조에서 데드 코드와 버그 가능성을 줄인다. 최신 실무 트렌드에서는 openapi, zod, tRPC 등과 결합하여 자동 타입 생성 및 검증에 적극적으로 활용되고 있다.

## 참고 자료

- [공식 문서 - Advanced Types](https://www.typescriptlang.org/docs/handbook/2/types-from-types.html)
- [Conditional Types 소개](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)
- [Mapped Types 공식 설명](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)
- [Template Literal Types](https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html)
- [Typescript Deep Dive (en)](https://basarat.gitbook.io/typescript/type-system/index-signatures)
- [tsconfig - 유틸리티 타입 모음](https://www.typescriptlang.org/docs/handbook/utility-types.html)

