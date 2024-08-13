```
-- 삽입 
SET user:email alex@gmail.com
GET user:email

-- 숫자 증가
SET user:count 1
GET user:count
INCR user:count
DECR user:count

-- 멀티 key set
MSET user:name alex user:email alex@gamil.com
MGET user:name user:email

-- 큐 스택 (연결리스트)
LPUSH user:list alex
LPUSH user:list brad
RPUSH user:list chad
RPUSH user:list dave

RPOP user:list

-- 길이, 몇번 째 값까지 뽑기
LLEN user:list
LRANGE user:list 0 2

-- Set : (문자열의 집합)
-- 중복을 혀용하지 않는다.
SADD user:java alex
SADD user:java brad
SADD user:java chad

-- 삭제
SREM user:java chad

-- 존재 여부
SISMEMBER user:java brad
SISMEMBER user:java chad

-- list 출력
SMEMBERS user:java

-- 개수 출력
SCARD user:java


-- 교집합 합집합
SADD user:python alex
SADD user:python dave
SADD user:java alex

-- 교집합
SINTER user:java user:python
-- 합집합
SUNION user:java user:python

-- 교집합 원소 개수 (숫자 = 키 개수)
SINTERCARD 2 user:java user:python

 -- ex) 중복을 허용하지 않는 방문자 수 세기
-- URL 을 키로 만들고 사용자 ID를 넣어준다.
-- 인증 토큰 블랙리스트


-- Hash : Field:Value 
-- hashCode() 
-- Hash = Map<str, Map<str,str>> 

HSET user:alex name alex age 25
HGET user:alex name
HMGET user:alex name age

HGETALL user:alex

HKEYS user:alex
HLEN user:alex

-- ex) 장바구니, 세션 정보
HSET cart:alex computer 1 mouse 2 keyboard 10

-- Sorted Set
-- 정렬된 집합 : 중복되지 않는 데이터 + 점수
-- ZADD key score value
ZADD user:ranks 10 alex
ZADD user:ranks 9 brad 11 chad
ZADD user:ranks 8 dave
ZADD user:ranks 9.5 eric

-- 증가
ZINCRBY user:ranks 2 alex

-- 해당 데이터의 위치
ZRANK user:ranks alex
ZRANK user:ranks eric
ZREVRANK user:ranks alex

-- 검색
ZRANGE user:ranks 0 3
ZREVRANGE user:ranks 0 3

-- ex) 순위표, Rate Limiter(접근 회수 제한 - 과도한 요청 거부)


-- DEL : key 삭제
SET somekey "to be deleted"
DEL somekey
DEL user:list


-- EXPIRE : 만료 시간 설정
SET expirekey "to be expired"
EXPIRE expirekey 50
-- EXPIRETIME : 만료 시간을 UNIX Timestamp로 반환
EXPIRETIME expirekey

-- KEYS : Key 검색
KEYS user:*

-- FLUSHDB : DB 초기화
FLUSHDB
```