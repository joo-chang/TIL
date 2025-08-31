타입 유틸리티(Partial, Pick, Omit, Record 등)
TypeScript는 반복적이거나 패턴화된 타입 변형을 간결하게 처리하기 위해 다양한 타입 유틸리티(내장 제네릭)을 제공한다. 대표적으로 `Partial`, `Pick`, `Omit`, `Record` 등은 객체 타입의 부분 적용, 속성 선택/제외, 맵핑 등의 작업을 매우 효율적으로 지원한다. 이를 활용하면 코드의 가독성과 재사용성, 타입 안전성이 크게 개선된다.

---

## 주요 개념

- **Partial<T>**: T 객체 타입의 모든 프로퍼티를 선택적(optional)으로 바꾼다.
- **Pick<T, K>**: T 객체에서 K(키 배열)로 지정한 속성만 골라서 새로운 타입을 만든다.
- **Omit<T, K>**: T 객체에서 K로 지정한 속성을 제외한 타입을 만든다.
- **Record<K, T>**: K라는 키 집합에 모두 T 타입 값을 할당하는 객체 타입을 만든다.
- 이 외에도 `Required`, `Readonly`, `Exclude`, `Extract`, `NonNullable` 등 많은 도우미 타입들이 존재한다.

---

## 실습 예제

### 실무 활용 사례

```typescript
interface User {
  id: number;
  name: string;
  email?: string;
  isActive: boolean;
}

// Partial: User 객체의 모든 속성이 optional
type UserUpdate = Partial<User>;
// => { id?: number; name?: string; email?: string; isActive?: boolean }

// Pick: name과 email만 골라 새 타입 생성
type UserContact = Pick<User, 'name' | 'email'>;
// => { name: string; email?: string }

// Omit: id를 제외한 성분만 선택
type UserWithoutId = Omit<User, 'id'>;
// => { name: string; email?: string; isActive: boolean }

// Record: string 키에만 boolean 값 할당
type Flags = Record<string, boolean>;
// => { [key: string]: boolean }
```

#### 고급 확장 예시

```typescript
// Omit의 제네릭 조합: 변경 불가(읽기 전용) 사용자 데이터 만들기
type ImmutableUser = Readonly<Omit<User, 'email'>>;
// => { readonly id: number; readonly name: string; readonly isActive: boolean }
```
- `Partial`, `Pick`, `Omit` 등은 복잡한 객체 타입을 부분 적용, 선택, 필드 제거 등 다양한 방식으로 쉽게 변환할 수 있다.
- `Record`는 맵(key-value) 구조 정의에 많이 쓰이며, 타입 자체도 동적으로 조합 가능하다.

---

## 정리/요약

타입 유틸리티는 대규모 프로젝트에서 객체 타입 변환을 간결하게 수행하고, 중복 코드와 오류 발생 가능성을 대폭 줄여준다. 실무 상황에서 다양한 타입 변형 요구를 매우 짧고 안전하게 처리할 수 있다는 것이 큰 강점이다.

---

## 참고 자료

- [TypeScript 공식 - 유틸리티 타입](https://www.typescriptlang.org/docs/handbook/utility-types.html)
- [MDN - TypeScript에서 타입 유틸리티 실전 활용](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object)
