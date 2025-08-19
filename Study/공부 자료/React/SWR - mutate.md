> mutate는 SWR의 캐시 데이터를 직접 수정하거나, 데이터를 강제로 리패칭(재요청)할 수 있게 해주는 함수이다.

1. 캐시 데이터 즉시 변경 (Optimistic UI)
2. 서버 데이터 강제 갱신 (Refetch)
3. 비동기 작업 후 캐시 동기화

이렁 상황에서 mutate를 사용한다.

## 기본 사용법

### useSwr 에서 반환되는 mutate

```ts
const { data, mutate } = useSWR('/api/user', fetcher);

// 예: 버튼 클릭 시 데이터 강제 갱신
<button onClick={() => mutate()}>데이터 새로고침</button>
```

인자 없이 호출하면 fetcher를 다시 실행해서 최신 데이터를 가져온다.

### 전역 mutate (import 해서 사용)

```ts
import useSWR, { mutate } from 'swr';

// 특정 키의 캐시를 전역에서 갱신
mutate('/api/user');
```

## Optimistic UI (낙관적 업데이트)

서버에 데이터 변경 요청을 보내기 전에, UI를 먼저 업데이트하고, 서버 응답에 따라 롤백할 수도 있다. (선적용 후뚜맞)

```ts
const { data, mutate } = useSWR('/api/user', fetcher);

const onUpdateName = async (newName) => {
	// 1. UI를 먼저 업데이트 (optimistic)
	mutate({ ...data, name: newName }, faslse);
	
	// 2. 서버에 변경 요청
	await fetch('/api/user', {
		method: 'POST',
		body: JSON.stringify({ name: newName })
	});
	
	// 3. 서버에서 최신 데이터 다시 받아오기
	mutate();
}
```

- 두 번째 인자 false : 서버에 요청하지 않고 캐시만 변경
- 마지막 mutate() : 서버에서 최신 데이터로 동기화

## 주요 옵션

1. mutate(key, data, false) : 캐시만 변경, 서버 요청 X
2. mutate(key) : 해당 키의 fetcher를 다시 실행 (서버에 데이터 재요청)
3. mutate(key, asyncFn) : 비동기 함수 실행 후, 결과로 캐시 업데이트

```ts
// 비동기 함수로 mutate
mutate('/api/user', async () => {
  const res = await fetch('/api/user');
  return res.json();
});
```

## 실무 활용 예시

1. 폼 제출 후, 리스트 데이터 즉시 반영
2. 좋아요 / 북마크 등 빠른 UI 반영
3. 여러 컴포넌트에서 동일 데이터 동기화
