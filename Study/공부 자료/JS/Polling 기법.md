Polling 기법은 주로 클라이언트가 서버에 일정 간격으로 요청을 보내서 최신 데이터를 받아오는 방식이다.

1. 클라이언트는 `setInterval` 이나 `setTimeout` 같은 타이머 함수를 사용해 주기적으로 서버에 HTTP 요청을 보낸다.
2. 서버는 요청을 받으면 현재 상태나 데이터를 응답한다.
3. 클라이언트는 응답을 받아 화면을 갱신하거나 필요한 처리를 한다.
4. 반복

## 구현 방법

### setInterval

```ts
function poll() {
	fetch('/api/messages/latest')
		.then(res => res.json())
		.then(data => {
			// 새 메세지 처리
		})
		.catch(console.error);
}

// 5초마다 poll 함수 실행
setInterval(poll, 5000);
```


### setTimeout 재귀 호출 방식 (더 유연)

```ts
function poll() {
	fetch('/api/messages/latest')
		.then(res => res.json())
		.then(data => {
			// 새 메세지 처리
			// 다음 요청 예약
			setTimeout(poll, 5000);
		})
		.catch(error => {
			//에러 발생 시에도 재시도
			setTimeout(poll, 5000);
		})
}

// 최초 호출
poll();
```

`setTimeout` 방식은 요청이 끝난 후 다음 요청을 예약하기 때문에, 요청이 지연되거나 실패해도 중첩 호출을 방지할 수 있다.

## 장점

- 구현이 쉽고 직관적
- 서버에 특별한 설정이 필요없음
- 모든 환경에서 동작 가능

## 단점

- 불필요한 요청이 많아 네트워크 낭비 발생
- 실시간성이 떨어짐 (주기 간격에 따라 지연 발생)
- 서버 부하 증가 (특히 사용자 수가 많을 때 문제)

## 최적화 방법

- 요청 간격 조절 : 상황에 따라 요청 간격을 늘리거나 줄여서 네트워크 부하를 조절
- 조건부 요청 : 서버에 마지막으로 받은 데이터의 타임스탬프나 ID를 보내서 변경된 데이터가 있을 때만 응답하도록 구현
- 에러 처리 및 재시도 로직 : 네트워크 오류 시 재시도 간격을 점진적으로 늘리는 백오프(backoff) 전략 사용

### 백오프 전략 에시

```ts
let retryCount = 0;
const maxRetry = 5;

function pollWithBackoff() {
  fetch('/api/data')
    .then(res => res.json())
    .then(data => {
      console.log('데이터:', data);
      retryCount = 0; // 성공 시 재시도 횟수 초기화
      setTimeout(pollWithBackoff, 5000); // 정상 폴링 간격
    })
    .catch(error => {
      console.error('에러 발생:', error);
      if (retryCount < maxRetry) {
        retryCount++;
        const backoffTime = Math.min(30000, 1000 * 2 ** retryCount); // 최대 30초
        setTimeout(pollWithBackoff, backoffTime);
      } else {
        console.log('최대 재시도 횟수 초과, 폴링 중단 또는 다른 처리');
      }
    });
}

pollWithBackoff();
```

- 재시도 간격 검진적 증가
- 최대 재시도 간격 제한
- 재시도 횟수 제한

위 과정을 통해 서버 과부화와 네트워크 문제를 완화할 수 있다.

## Polling과 대안 기법 비교

|기법|설명|장점|단점|
|---|---|---|---|
|Polling|주기적으로 서버에 요청을 보내 상태 확인|구현 간단, 모든 환경에서 동작|비효율적, 지연 발생 가능|
|Long Polling|서버가 새 데이터가 생길 때까지 응답 지연 후 전송|실시간성 개선, 불필요한 요청 감소|서버 연결 유지 부담, 구현 복잡도 증가|
|WebSocket|클라이언트-서버 간 양방향 실시간 통신 유지|실시간성 최고, 양방향 통신 가능|서버 및 클라이언트 구현 복잡, 리소스 소모|
|Server-Sent Events (SSE)|서버가 클라이언트에 단방향 실시간 이벤트 푸시|구현 간단, HTTP 기반, 실시간성 좋음|양방향 통신 불가, 일부 브라우저 제한 가능|

