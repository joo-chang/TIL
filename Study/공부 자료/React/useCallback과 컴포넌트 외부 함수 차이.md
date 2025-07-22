## usCallback

useCallbakc 은 리액트 훅으로, 함수를 메모이제이션해서 컴포너늩가 리렌더링될 때마다 같은 함수 인스턴스를 재사용하게 해준다.

주로 자식 컴포넌트에 props로 함수를 넘길 때, 불필요한 리렌더링을 방지하기 위해 사용한다.

```ts
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);

```

위에서 a or b 가 바뀌지 않으면, 함수는 새로 생성되지 않고 이전 함수를 재사용한다.

## 컴포넌트 외부에서 함수 생성

컴포넌트 외부에 함수를 선언하면, 컴포넌트가 리렌더링되어도 함수는 항상 같은 인스턴스를 가진다.

```ts
const externalFunction = () => {
  console.log('Hello');
};

function MyComponent() {
  return <button onClick={externalFunction}>Click me</button>;
}

```

이 경우 externulFunction은 컴포넌트 리렌더링과 무관하게 항상 같은 함수이다.

## 요약

- 컴포넌트 상태나 props에 의존하는 함수라면 `useCallback`을 써서 메모이제이션하는 게 좋다.
- 상태나 props에 전혀 의존하지 않는 고정 함수라면 컴포넌트 외부에 선언해서 재사용하는 게 더 간단하고 효율적이다.

