interface는 객체의 형태를 확장하는 데 용이한 반면, type은 튜플, 인터섹션, 유니온 등을 이용하여 더 복잡한 타입 정의 및 조합을 표현하는 데 용이하다.

## interface

- interface는 선언 병합을 지원해 여러 번 선언할 수 있어, 주로 객체 타입을 확장할 때 유리하다.
- 동일한 이름을 가진 interface를 여러 번 선언하면, 자동으로 속성들이 합쳐진다.\
- 다른 interface를 `extends` 키워드로 확장할 수 있다.

### 예시 (복잡한 객체 구조와 확장)

```ts
// 기본 인터페이스
interface Address {
  street: string;
  city: string;
  country: string;
}

// 사용자 기본 정보 인터페이스
interface User {
  id: number;
  name: string;
  email: string;
  address: Address;
  getContactInfo(): string;
}

// 관리자 인터페이스는 User를 확장하고 추가 속성 포함
interface Admin extends User {
  role: "superadmin" | "admin" | "moderator";
  permissions: string[];
}

// 함수 예시
function printUserInfo(user: User) {
  console.log(`User: ${user.name}, Email: ${user.email}`);
  console.log(`Address: ${user.address.street}, ${user.address.city}, ${user.address.country}`);
  console.log(`Contact: ${user.getContactInfo()}`);
}

const adminUser: Admin = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
  address: {
    street: "123 Main St",
    city: "Seoul",
    country: "South Korea",
  },
  getContactInfo() {
    return `${this.email} / ${this.address.city}`;
  },
  role: "superadmin",
  permissions: ["read", "write", "delete"],
};

printUserInfo(adminUser);
console.log(`Role: ${adminUser.role}`);
console.log(`Permissions: ${adminUser.permissions.join(", ")}`);

```

## type

- 원시 타입, 유니언 타입, 튜플 등 다양한 타입을 정의할 수 있다.
- 선언 병합 불가
- 다른 타입을 `&` 연산자로 교차 타입(intersection type)으로 확장할 수 있다.


### 예시 (유니온, 교차, 튜플)

```ts
// 기본 타입 정의
type ID = string | number;

type Coordinates = [number, number]; // 튜플 타입

type Contact = {
  phone?: string;
  email: string;
};

// User 타입 정의 (교차 타입 사용)
type User = {
  id: ID;
  name: string;
  contact: Contact;
} & {
  location: Coordinates;
  isActive: boolean;
};

// 유니언 타입 예시
type Admin = User & {
  role: "admin" | "superadmin";
  permissions: string[];
};

type Guest = {
  guestId: ID;
  visitReason: string;
};

// 유니언 타입으로 여러 사용자 타입 표현
type Person = Admin | Guest;

// 함수 예시
function getPersonInfo(person: Person) {
  if ("role" in person) {
    console.log(`Admin: ${person.name}, Role: ${person.role}`);
  } else {
    console.log(`Guest ID: ${person.guestId}, Reason: ${person.visitReason}`);
  }
}

const admin: Admin = {
  id: 101,
  name: "Bob",
  contact: { email: "bob@example.com" },
  location: [37.5665, 126.978],
  isActive: true,
  role: "admin",
  permissions: ["read", "write"],
};

const guest: Guest = {
  guestId: "G-2025",
  visitReason: "Meeting",
};

getPersonInfo(admin);
getPersonInfo(guest);

```

