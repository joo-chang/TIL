> TypeScript의 Utility Types는 기존 타입을 변형하거나 조합해 새로운 타입을 쉽게 만들 수 있도록 도와준다.
> Partial, Pick, Omit 등은 코드의 재사용성과 유지보수성을 높여주며, 실무에서 타입 리팩토링이나 API 응답 타입 정의에 자주 활용된다.

## 주요 개념

- `Partial<T>` : T 타입의 모든 프로퍼티를 Optional로 만든다.
- `Pick<T,K>` : T 타입에서 K에 해당하는 프로퍼티만 선택해 새로운 타입을 만든다.
- `Omit<T,K>` : T 타입에서 K에 해당하는 프로퍼티를 제외한 타입을 만든다.
- 폼 입력, API 요청, 일부 필드만 사용하는 컴포넌트 등에서 자주 사용된다.

## 예제

### 기본 활용 예시

```ts
// 원본 타입
type User = {
  id: number;
  name: string;
  email: string;
  isAdmin: boolean;
};

// Partial: 모든 필드를 optional로
type UserUpdate = Partial<User>;
// Pick: id와 name만 선택
type UserSummary = Pick<User, 'id' | 'name'>;
// Omit: isAdmin만 제외
type UserPublic = Omit<User, 'isAdmin'>;
```

1. UserUpdate는 PATCH API 요청 등에서 일부 필드만 보낼 때 사용
2. UserSummary는 리스트 등에서 일부 정보만 필요할 때 유용
3. UserPublic은 민감 정보(isAdmin 등)를 제외한 타입을 만들 때 활용

## 고급 구현 : 폼 리팩토링 및 커스텀 유틸리티 타입

```ts
// 폼 입력에 필요한 필드만 Pick
type UserFormInput = Pick<User, 'name' | 'email'>;

// 커스텀 유틸리티: 특정 필드만 필수로 만들기
type RequireOnly<T, K extends keyof T> = Partial<T> & Pick<T, K>;

// name만 필수, 나머지는 optional
type NameRequiredUser = RequireOnly<User, 'name'>;
```

1. UserFormIput은 회원가입/수정 폼에서 필요한 필드만 추출해 사용
2. RequireOnly는 일부 필드만 필수로 만들고 싶을 때 유용

## 사용 이유
### 기존의 불편함

- 중복 타입 선언 : 비슷한 타입을 여러 번 선언해야 해서, 코드가 길어지고 유지보수가 어려움.
- 부분 업데이트/선택적 필드 처리의 번거로움 : PATCH API, 폼 등에서 일부 필드만 필요할 때, 매번 새로운 타입을 만들어야 했음.
- 민감 정보 제거의 불편함 : 특정 필드만 제외하거나 포함하는 타입을 만들 때, 수동으로 타입을 다시 정의해야 했음.

### Utility Types가 해결한 점

- Partial, Pick, Omit 등으로 기존 타입을 쉽게 변형/조합 가능.
- 코드 중복 최소화, 유지보수성 향상.
- 실무에서 자주 발생하는 "일부 필드만 필요"한 상황에 빠르고 안전하게 대응.

---

## 요약

1. Utility Types는 타입 재사용성과 유지보수성을 크게 높여준다.
2. Partial, Pick, Omit 등은 실무에서 폼, API, 컴포넌트 등 다양한 곳에서 활용된다.
3. 커스텀 유틸리티 타입을 만들어, 복잡한 타입 요구사항도 쉽게 대응할 수 있다.

