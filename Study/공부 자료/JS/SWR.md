> SWR은 Vercel 에서 만든 React용 데이터 패칭 라이브러리로 "Stale-While-Revalidate" 전략을 구현한다.
> 즉, 캐시된 데이터를 우선 보여주고, 백그라운드에서 최신 데이터를 가져와 자동으로 갱신한다.
> 이 방식은 UX를 크게 개선하며, 최신 React 생태계에서 서버 상태 관리의 표준으로 자리 잡고 있다.


## 주요 개념

1. Stale-While-Revalidate : 캐시된 데이터를 즉시 반환하고, 동시에 최신 데이터를 받아와 갱신한다.
2. 자동 캐싱 및 동기화 : 동일한 키로 여러 컴포넌트에서 데이터를 요청하면, SWR이 내부적으로 캐시를 공유하고 동기화한다.
3. 자동 리패칭 : 브라우저 포커스 변경, 네트워크 재연결 등 이벤트 발생 시 자동으로 데이터를 다시 가져온다.
4. 간단한 API : `useSWR` 훅 하나로 데이터 패칭, 캐싱, 에러처리, 로딩 상태 관리가 가능하다.
5. 확장성 : 글로벌 설정(SWRConfig), 미들웨어, 커스텀 Fetcher, mutate 등 다양한 확장 기능을 제공한다.

## 기본 사용법

```ts
import useSWR from 'swr';

// fetcher 함수: 데이터를 어떻게 가져올지 정의
const fetcher = url => fetch(url).then(res => res.json());

function UserProfile() {
  // useSWR(키, fetcher, 옵션)
  const { data, error, isLoading } = useSWR('/api/user', fetcher);

  if (isLoading) return <div>로딩 중...</div>;
  if (error) return <div>에러 발생</div>;
  return <div>안녕하세요, {data.name}님!</div>;
}
```

1. fetcher는 데이터를 가져오는 함수(보통 fetch, axios 등 사용)
2. useSWR의 첫 번째 인자는 캐시 키(동일 키면 캐시 공유)
3. isLoading, error, data 등 상태값을 통해 UI를 제어

## 글로벌 설정 및 옵션 적용법

SWRConfig를 사용하면 fetcher, 에러 핸들러 등 전역 옵션을 설정할 수 있다.

```ts
import { SWRConfig } from 'swr';

function App() {
  return (
    <SWRConfig
      value={{
        fetcher: (url) => fetch(url).then(res => res.json()),
        onError: (err) => console.log('글로벌 에러:', err),
        revalidateOnFocus: false, // 포커스 시 자동 리패치 비활성화
        dedupingInterval: 2000,   // 2초 내 중복 요청 방지
      }}
    >
      <UserProfile />
    </SWRConfig>
  );
}
```

1. 여러 컴포넌트에서 동일한 fetcher, 옵션을 공유할 수 있다.
2. onError, onSuccess 등 콜백으로 로깅, 에러 처리 등 커스터마이징 가능

## 요약

1. SWR은 React에서 서버 상태를 효율적으로 관리할 수 있는 데이터 패칭 라이브러리이다.
2. Stale-While-Revalidate 전략으로 UX를 개선하고, 캐싱/동기화/자동 리패칭 등 실무에 유용한 기능을 제공한다.
3. 글로벌 설정, 다양한 옵션, 커스텀 fetcher 등 확장성이 뛰어나며, Next.js 등 최신 프레임워크와도 잘 어울린다.
