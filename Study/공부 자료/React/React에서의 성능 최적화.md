# React에서의 성능 최적화

## 1. 서론(개요)
React는 컴포넌트 기반의 UI 라이브러리로, 대규모 애플리케이션에서도 효율적으로 동작하도록 설계되었다. 그러나 잘못된 렌더링, 불필요한 상태 관리, 비효율적인 코드 구조 등으로 인해 성능 저하가 발생할 수 있다. 실무에서는 다양한 최적화 기법을 통해 렌더링 성능과 사용자 경험을 개선하는 것이 중요하다.

## 2. 주요 개념
- **불필요한 렌더링 방지**: React의 Virtual DOM은 효율적이지만, props나 state가 변경될 때마다 컴포넌트가 불필요하게 렌더링될 수 있다.  
- **메모이제이션**: `React.memo`, `useMemo`, `useCallback`을 활용해 컴포넌트와 함수, 값의 재생성을 방지한다.
- **코드 스플리팅**: `React.lazy`, `Suspense`를 사용해 필요한 시점에만 코드(컴포넌트)를 로드한다.
- **리스트 렌더링 최적화**: key 속성의 적절한 사용, 가상화(react-window, react-virtualized 등)로 대량 데이터 렌더링 성능을 높인다.
- **이미지 및 리소스 최적화**: 이미지 지연 로딩, 최적 포맷 사용, CDN 활용 등으로 네트워크 비용을 줄인다.

## 3. 실습 예제

### (1) 불필요한 렌더링 방지 및 메모이제이션

```jsx
import React, { useState, useCallback, memo } from "react";

// 자식 컴포넌트에 React.memo 적용
const Child = memo(({ onClick, value }) => {
  console.log("Child 렌더링");
  return <button onClick={onClick}>{value}</button>;
});

function Parent() {
  const [count, setCount] = useState(0);
  const [text, setText] = useState("");

  // useCallback으로 함수 메모이제이션
  const handleClick = useCallback(() => {
    setCount((c) => c + 1);
  }, []);

  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <Child onClick={handleClick} value={count} />
    </div>
  );
}
```
**주석 설명:**  
- `Child` 컴포넌트는 `React.memo`로 감싸 불필요한 렌더링을 방지한다.  
- `handleClick` 함수는 `useCallback`으로 메모이제이션하여, `text`가 변경되어도 `Child`의 렌더링이 발생하지 않는다.

### (2) 코드 스플리팅과 동적 import

```jsx
import React, { Suspense, lazy } from "react";

const HeavyComponent = lazy(() => import("./HeavyComponent"));

function App() {
  return (
    <div>
      <h1>코드 스플리팅 예제</h1>
      <Suspense fallback={<div>로딩 중...</div>}>
        <HeavyComponent />
      </Suspense>
    </div>
  );
}
```
**주석 설명:**  
- `HeavyComponent`는 실제로 필요할 때만 로드된다.  
- `Suspense`의 fallback UI로 로딩 상태를 표시한다.

### (3) 리스트 가상화(react-window 활용)

```jsx
import { FixedSizeList as List } from "react-window";

const Row = ({ index, style }) => (
  <div style={style}>Row {index}</div>
);

function VirtualizedList() {
  return (
    <List
      height={150}
      itemCount={1000}
      itemSize={35}
      width={300}
    >
      {Row}
    </List>
  );
}
```
**주석 설명:**  
- 1000개의 아이템을 한 번에 렌더링하지 않고, 화면에 보이는 부분만 렌더링하여 성능을 극대화한다.

## 4. 정리/요약
React에서의 성능 최적화는 불필요한 렌더링 방지, 메모이제이션, 코드 스플리팅, 리스트 가상화 등 다양한 기법을 조합하여 이루어진다. 최신 실무에서는 react-window, React.memo, Suspense 등 최신 API와 라이브러리를 적극적으로 활용하는 것이 권장된다. 성능 병목 구간을 파악하기 위해 React DevTools의 Profiler 등 도구를 함께 사용하는 것이 좋다.

## 5. 참고 자료
- [React 공식 성능 최적화 가이드](https://react.dev/learn/optimizing-performance)
- [React.memo 공식 문서](https://react.dev/reference/react/memo)
- [react-window 공식 문서](https://react-window.vercel.app/)
- [React Profiler 사용법](https://react.dev/reference/react/Profiler)

