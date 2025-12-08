```ts
queueMicrotask(() => {
      onAnswerChange?.({
        type: content.type,
        answer: value,
      });
});
```

비동기 업데이트: queueMicrotask()를 사용하여 상태 업데이트 후 콜백 호출을 다음 이벤트 루프로 지연

- 이유: React 상태 업데이트와 부모 콜백 호출의 타이밍 충돌 방지  

---

