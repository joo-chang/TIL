# 멀티 프로세스와 멀티 스레드 비교

## 멀티 프로세스

멀티 프로세스는 운영체제에서 하나의 응용 프로그램에 대해 동시에 여러 개의 프로세스를 실행할 수 있게 하는 기술을 말한다.
보통 하나의 프로그램은 하나의 프로세스가 메모리에 생성되지만 부가 기능을 위해 여러 개의 프로세스를 생성하는 것이다.

### 장점

#### 프로그램 안전성

멀티 프로세스는 각 프로세스가 독립적인 메모리 공간을 가지므로, 한 프로세스가 비정상적으로 종료되어도 다른 프로세스에 영향을 주지 않는다. 따라서 프로그램 전체의 안전성을 확보할 수 있다.

#### 프로그램 병렬성

멀티 프로세스와 여러 개의 CPU 코어를 활용하여, 다중 CPU 시스템에서 각 프로세스를 병렬적으로 실행하여 성능을 향상 시킬 수 있다.

이 장점은 멀티 스레드의 장점이기도 하다.

#### 시스템 확장성

멀티 프로세스는 각 프로세스가 독립적이므로, 새로운 기능이나 모듈을 추가하거나 수정할 때 다른 프로세스에 영향을 주지 않는다. 따라서 시스템 규모를 쉽게 확장할 수 있다.

### 단점

#### Context Switching Overhead

멀티 태스킹 구성의 핵심 기술인 컨텍스트 스위칭 과정에서 성능 저하가 올 수있다. 
특히 프로세스를 컨텍스트 스위칭하면 `CPU는 다음 프로세스의 정보를 불러오기 위해 메모리를 검색하고, CPU 캐시 메모리를 초기화하며, 프로세스 상태를 저장하고, 불러올 데이터를 준비해야하기 때문에` 빈번한 컨텍스트 스위칭 작업으로 인해 오버헤드가 발생할 수 있다.
반면에, 스레드를 컨텍스트 스위칭하면 프로세스 스위칭보다 가벼워 훨씬 빠르고 좋다.

따라서, 멀티 프로세스 환경에서는 Context Switching Overhead를 최소화하는 방법이 중요하다.
프로세스 수를 적정하게 유지하거나, I/O 바운드 작업이 많은 프로세스와 CPU 바운드 작업이 많은 프로세스를 분리하여 관리하고, CPU 캐시를 효율적으로 활용하는 등의 방법을 고려해 봐야 한다.

#### 자원 공유 비효율성

멀티 프로세스는 각 프로세스가 독립적인 메모리 공간을 가지므로, 메모리 사용량이 증가하게 된다.
각 프로세스 간 자원 공유가 필요할 경우 어렵고 복잡한 통신 기법인 IPC(Inter-Process Commnuication) 를 사용해야 한다.
IPC는 실행 중인 프로세스 간에 정보를 주고받는 메커니즘을 말한다. 이를 위해 파이프, 소켓, 메세지 큐 등 다양한 방법이 사용된다. 그런데 IPC 자체로 오버헤드가 발생한다. 예를 들어, 파이프나 소켓과 같은 IPC 기법은 데이터를 복사하거나 버퍼링하는 과정에서 성능 저하가 발생할 수 있기 때문이다. 또한 코드의 복잡도를 증가시킨다.

<br>

---
## 멀티 스레드

스레드는 하나의 프로세스 내에 있는 실행 단위이다. 멀티 스레드는 하나의 프로세스 안에 여러 개의 스레드가 있어, 두 가지 이상의 동작을 동시에 처리하도록 하는 것이다.

멀티 프로세스와의 차이점은 웹 브라우저로 예시를 들어보면 웹 브라우저에서 여러 탭이나 여러 창이 각각 프로세스로 멀티 프로세스이고, 멀티 스레드는 단일 탭 내에서 브라우저 이벤트 루프, 네트워크 처리, I/O 및 기타 작업을 관리하고 처리하는 데 사용된다.

### 장점

많은 운영체제들이 멀티 프로세싱을 지원하지만 멀티 스레드를 기본으로 하고 있다. 

#### 프로세스보다 가벼움

스레드는 프로세스 내에서 생성되기 때문에 실행 환경 설정 작업이 매우 간단하여 생성 및 종료가 빠르다.
또한, 코드, 데이터, 힙 영역을 서로 공유하기 때문에 데이터 용량이 프로세스보다 작다.

#### 자원의 효율성

멀티 스레드는 하나의 프로세스 내에서 생성되기 때문에, 공유 메모리에 대해 스레드 간 자원 공유가 가능하다. 

#### Context Switching 비용 감소

스레드도 컨텍스트 스위칭 오버헤드가 존재하지만, 프로세스 컨텍스트 스위칭에 비하면 훨씬 낮다.
프로세스 컨텍스트 스위칭 시 CPU 캐시를 모두 초기화하고, 새로운 프로세스 정보를 적재해야 하므로 높은 비용이 든다. 하지만 스레드는 공유 자원을 제외한 스레드 정보(stack, register) 만 교체하면 되므로 상대적으로 낮다.

#### 응답 시간 단축

앞선 장점들로 인해 응답 시간이 빠르다.

### 단점

#### 안정성 문제

멀티 스레드는 하나의 스레드에서 문제가 발생하면 다른 스레드들도 영향을 받아 전체 프로그램이 종료될 수 있다.
하지만 이 문제는 적절한 예외 처리, 에러 발생 시 새로운 스레드를 생성하거나 스레드 풀에서 잔여 스레드를 가져오는 등의 방법으로 해결할 수 있다.

#### 동기화로 인한 성능 저하

멀티 스레드는 여러 개의 스레드가  공유 자원에 동시 접근할 수 있기 때문에 동기화 문제가 발생할 수 있다. 따라서 스레드 간 동기화(syncronized)는 데이터 접근을 제어하기 위한 필수적인 기술이다.

동기화 작업은 여러 스레드 접근을 제한하는 것이기 때문에 병목 현상이 일어나 성능 저하될 가능성이 높다.

이를 해결하기 위해 임계 영역에 대해 뮤텍스, 세마포어 방식을 활용한다.

#### 데드락 (교착 상태)

데드락은 다수의 프로세스나 스레드가 서로의 자원을 점유하고 무한정 자원을 기다리는 상황에서 발생하는 교착상태를 말한다.

데드락 방지를 위해 상호배제, 점유와 대기, 비선점, 순환대기 등의 알고리즘을 사용하면 된다.

#### Context Switching Overhead

컨텍스트 스위칭 오버헤드 비용 자체를 무시할 수는 없다. 스레드 수가 많을 수록 컨텍스트 스위칭이 많이 발생하고, 성능 저하로 이어질 수 있다.

