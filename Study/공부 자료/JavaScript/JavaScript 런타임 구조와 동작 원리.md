> JavaScript 런타임 구조와 동작 원리는 자바스크립트가 단일 스레드 기반으로 동작하면서도 비동기 I/O, 이벤트 처리, 콜백 등의 기능을 어떻게 효율적으로 처리하는지 설명하는 핵심 개념이다.  
> 실무에서는 런타임 구조 이해가 비동기 처리, 성능 최적화, 메모리 관리 등 다양한 영역에서 중요하게 작용한다.

## 주요 개념

- **엔진(Engine)과 런타임 환경(Runtime Environment)**  
  엔진(V8, SpiderMonkey 등)은 자바스크립트 코드를 파싱, 컴파일(JIT), 실행하는 핵심 컴포넌트다. 런타임 환경은 엔진과, 이벤트 루프(Event Loop), 콜 스택(Call Stack), 콜백 큐(Queue), Web APIs(브라우저 환경), Node APIs(Node.js 환경) 등 엔진 외부의 유틸리티를 포함한다.

- **콜 스택(Call Stack)**  
  함수 실행 시 쌓이는 실행 컨텍스트(Execution Context)들의 구조다. 가장 위의 프레임부터 순차적으로 처리한다. 동기 실행은 콜 스택에서 바로 처리된다.

- **이벤트 루프(Event Loop)**  
  자바스크립트의 싱글 스레드 기반에서 비동기 동작(CB/PROMISE 등)을 지원하는 핵심 메커니즘이다. 콜 스택이 비면 대기 중인 큐에서 태스크를 꺼내 실행한다.  
  마이크로태스크 큐(Promise/MutationObserver 등)와 매크로태스크 큐(setTimeout, I/O 등)가 따로 관리돼 우선순위가 정해진다.

- **Web APIs / Node APIs**  
  타이머, Ajax, 파일 IO 등은 JS 엔진이 직접 실행하지 않고, 브라우저(또는 Node.js)가 제공하는 별도 기능을 먼저 처리한다.

## 실무 활용 예제

아래는 “이벤트 루프, 콜 스택, 태스크 큐”의 구조적 동작을 이해하기 위한 간단하지만 구체적인 실습 예제다.

```js
console.log('1'); // (동기) 콜 스택에 즉시 push

setTimeout(() => {
  console.log('2'); // (비동기) 매크로태스크 큐에 push, 최소 0ms 이후 실행
}, 0);

Promise.resolve().then(() => {
  console.log('3'); // (비동기) 마이크로태스크 큐에 push, 콜 스택이 비면 우선 처리
});

console.log('4'); // (동기) 콜 스택에 push
```

**실행 순서 해설**  
1. ‘1’과 ‘4’는 동기로 즉시 출력된다.  
2. `Promise.then`은 마이크로태스크 큐로, `setTimeout`은 매크로태스크 큐로 이동한다.  
3. 콜 스택이 비자, 마이크로태스크(‘3’)가 먼저 실행되고,  
4. 다음에 매크로태스크(‘2’)가 실행된다.

**실행 결과**
```
1
4
3
2
```

## 고급 구현 방법/확장 예시

최신 실무에서는 비동기 작업을 효율적으로 다루기 위해 마이크로태스크와 매크로태스크 우선순위를 이용하거나,  
Node.js에서는 process.nextTick, setImmediate, Promise 등을 적절히 조합해 병목을 줄이고 응답성을 높인다.

```js
Promise.resolve().then(() => console.log('Promise'));
process.nextTick(() => console.log('nextTick'));
setImmediate(() => console.log('setImmediate'));
setTimeout(() => console.log('setTimeout'), 0);

// node.js 런타임에서 실행할 때의 출력: 
// nextTick → Promise → setTimeout → setImmediate (환경에 따라 미묘하게 다름)
```

- process.nextTick은 마이크로태스크보다 우선 실행된다.
- setImmediate는 setTimeout(0)과 다르게 이벤트 루프의 다음 사이클에서 실행된다.

## 정리/요약

JavaScript 런타임 구조는 싱글 스레드 기반에서도 비동기 처리를 자연스럽게 구현하기 위해  
콜 스택, 이벤트 루프, 태스크 큐, Web/Node APIs 등 다양한 컴포넌트가 조합되는 방식이다.  
실무에서는 각 동작 원리를 정확히 이해하고, 비동기/동기 처리 흐름과 우선순위를 기반으로 성능 최적화, 디버깅 등을 수행해야 한다.

## 참고 자료

- [MDN - JavaScript 런타임 환경 구조](https://developer.mozilla.org/ko/docs/Web/JavaScript/EventLoop)
- [Node.js Event Loop 공식 문서](https://nodejs.org/ko/docs/guides/event-loop-timers-and-nexttick)
- [JavaScript 엔진 비교 및 최신 동향](https://v8.dev/docs)
