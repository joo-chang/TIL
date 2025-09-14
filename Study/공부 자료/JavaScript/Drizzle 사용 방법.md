# Drizzle 사용 방법

Drizzle은 이더리움 스마트 컨트랙트와 프론트엔드 애플리케이션(주로 React)을 연결하기 위한 JavaScript 라이브러리다. 상태 관리, 데이터 동기화, 트랜잭션 추적 등을 일관성 있고 효율적으로 지원한다. 본 정리는 최신 Drizzle(Truffle Suite 기반) 기준으로 작성한다.

## 주요 개념

- Drizzle은 프론트엔드에서 이더리움 스마트 컨트랙트의 상태와 데이터를 자동으로 동기화하는 역할을 수행한다.
- Drizzle Store는 Redux를 기반으로 하며, 컨트랙트 상태, 계정 정보, 트랜잭션 결과 등을 자동으로 관리한다.
- 최신 실무에서는 React Hooks와 연계해 Drizzle을 사용하는 사례가 많으며, 표준 Redux store처럼 접근할 수 있다.

## 실습 예제

### Drizzle 환경 설정 및 스마트 컨트랙트 연동 예제

```bash
npm install @drizzle/store @drizzle/react-plugin web3
```

### Drizzle 옵션 및 인스턴스화

```js
// src/drizzleOptions.js
import SimpleStorage from './contracts/SimpleStorage.json';

export const drizzleOptions = {
  contracts: [SimpleStorage],
  web3: {
    fallback: {
      type: "ws",
      url: "ws://127.0.0.1:8545"
    }
  }
};
```

### React에서 Drizzle 적용

```jsx
import React from "react";
import { Drizzle } from "@drizzle/store";
import { DrizzleProvider } from "@drizzle/react-plugin";
import { drizzleOptions } from "./drizzleOptions";
import MyComponent from "./MyComponent";

// Drizzle 인스턴스 생성
const drizzle = new Drizzle(drizzleOptions);

function App() {
  return (
    <DrizzleProvider drizzle={drizzle}>
      <MyComponent />
    </DrizzleProvider>
  );
}
```

### 컨트랙트 상태 접근 (Hook 사용 예시)

```jsx
import { useDrizzle, useDrizzleState } from "@drizzle/react-plugin";

export default function MyComponent() {
  const { drizzle } = useDrizzle();
  const state = useDrizzleState((state) => state);

  // SimpleStorage 컨트랙트 값 읽기
  const value =
    state.contracts?.SimpleStorage?.fields?.storedData?.value || "로딩 중…";

  // 트랜잭션 실행
  const setStorage = () => {
    drizzle.contracts.SimpleStorage.methods.set(100).send();
  };

  return (
    <div>
      <div>저장된 값: {value}</div>
      <button onClick={setStorage}>저장 값 100으로 변경</button>
    </div>
  );
}
```

- DrizzleProvider, useDrizzle, useDrizzleState를 함께 사용하면 React 상태 관리와 같이 스마트 컨트랙트 데이터를 활용할 수 있다.
- 트랜잭션 요청 후 자동으로 상태가 갱신된다.

## 확장 구현: 커스텀 훅(Custom Hook) 패턴

```jsx
import { useCallback } from "react";
import { useDrizzle, useDrizzleState } from "@drizzle/react-plugin";

export function useSimpleStorage() {
  const { drizzle } = useDrizzle();
  const value = useDrizzleState(
    (state) => state.contracts?.SimpleStorage?.fields?.storedData?.value
  );

  const setStorage = useCallback(
    (newVal) => drizzle.contracts.SimpleStorage.methods.set(newVal).send(),
    [drizzle]
  );

  return { value, setStorage };
}
```

- 커스텀 훅 패턴을 사용하면 컴포넌트 간 Drizzle 데이터 관리 로직의 재사용성과 가독성을 높일 수 있다.

## 정리/요약

Drizzle은 이더리움 스마트 컨트랙트의 상태와 데이터를 프론트엔드에서 일관적으로 관리할 수 있게 해 주는 강력한 툴이다. React와 Redux 환경에 원활하게 통합되며, 데이터 동기화, 트랜잭션 상태 추적을 자동화할 수 있다. 커스텀 훅 등 최신 React 개발 패턴과의 궁합도 뛰어난 것이 실무 장점이다.

## 참고 자료

- [공식 Drizzle 문서 (Truffle Suite)](https://www.trufflesuite.com/docs/drizzle/overview)
- [드리즐 React 플러그인 GitHub](https://github.com/trufflesuite/drizzle/tree/master/react-plugin)
- [Web3와 Drizzle로 풀스택 Dapp 만들기](https://blog.logrocket.com/building-a-full-stack-dapp-using-web3-and-drizzle/)
