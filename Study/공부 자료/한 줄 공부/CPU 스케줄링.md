## 프로세스 상태

![[Pasted image 20240318175342.png]]
### New
생성 되었지만 운영체제에 의해 수행 가능한 프로세스 풀로 진입이 아직 허용되지 않은 프로세스

### Ready
기회가 주어지면 수행될 준비가 되어있는 프로세스

### Running
현재 수행 중인 프로세스

### Blocked (블록/대기)
I/O 작업(입출력 연산) 완료 등과 같은 이벤트가 발생할 때까지 수행될 수 없는 프로세스

### Exit
프로세스 수행이 종료되거나, 어떤 이유로 중단되어 프로세스 풀에서 방출된 프로세스

### 디스패처 (Dispatcher) 란?

CPU를 한 프로세스로부터 다른 프로세스로 교체해주는 프로그램이다.

쉽게말해, 프로그램이 실행되다 중단되고, 다음 프로그램을 실행하게 해주는 프로그램이라고 볼 수 있다.

Ready -> Running 단계에서 스케줄러 또는 디스패처가 준비 상태에 있는 프로세스 중 하나를 선택해 실행하게 한다.

## CPU 스케줄링 

> 운영체제가 프로세스에 합리적으로 CPU 자원을 할당하는 과정을 의미한다.
> Ready 상태의 프로세스 중에서 어떤 프로세스에게 CPU를 할당할지 결정하는 것을 CPU 스케줄링이라고 한다.

Dispatcher는 CPU 제어권을 CPU 스케줄러에 의해 선택된 프로세스에게 넘기는데, 이를 Context Switch(문맥 교환)라고 한다.

### 목적

CPU 스케줄링 목적은 모든 프로세스가 적당히, 공평하게, 효율적으로 자원을 할당받는 것이다. 하지만 다양한 상황에서 만족 시키기는 쉽지 않다. 그래서 자연스럽게 이를 위한 고려사항들을 떠올리고, 그에 맞는 알고리즘들이 설계되었다.

- 공평성 : 프로세스에게 자원을 배분하는 과정은 공평해야한다.
- 효율성 : 시스템 자원이 쉬는 시간이 없어야 한다. (CPU를 놀게 하면 안됨)
- 안정성 : 중요 프로세스들은 우선권을 주어야 한다. 또한 프로세스가 증가해도 안정적으로 돌아가야 한다.
- 반응 시간 보장 : 응답이 없는 경우 사용자는 시스템이 멈춘 것으로 가정하기 때문에 적절한 시간 안에 프로세스의 요구에 반응해야 한다.
- 무한 연기 방지 : 특정 프로세스의 작업이 무한히 연기되어서는 안된다.


OS의 Scheduler란 컴퓨터의 자원을 활용해서 일을 할당하는 개념으로, 크게 세가지로 나뉜다.

- Long-term scheduler: 디스크에서 메모리로 프로세스를 스케줄링
- Medium-term scheduler: 메모리와 디스크 사이의 스왑 관리
- Short-term scheduler: CPU와 메모리 사이의 스케줄링

우리가 주목할 것은 Short-term scheduler이다.

메모리에 있는 프로세스를 CPU로 보내는 것이 Short-term scheduler이다.

![[Pasted image 20240319211724.png]]

 해야할 일을 정하는 것(어떤 프로세스를 선택할지) 까지가 Scheduler의 일, 실제로 cpu를 Process에 할당하는 것이 Dispathcer의 일이다.
 
![[Pasted image 20240319211749.png]]

그래서 넓은 의미의 Scheduling은 Scheduler와 Dispatcher 기능을 동시에 말한다.


# CPU 스케줄링 종류

## 선점형 스케줄링(preemptive scheduling)

한 프로세스가 CPU를 사용 중일 때, 더 높은 우선순위를 가진 프로세스가 도착하면 운영체제가 현재 실행중인 프로세스를 중지시키고, 새로운 프로세스에 CPU를 할당할 수 있다.

- 우선 순위가 높은 프로세스를 빠르게 처리해야할 경우 유용하다.
- 선점이 일어날 경우, 오버헤드가 발생하며 처리시간을 예측하기 힘들다.
- 잦은 Context Switching이 일어날 경우 오버헤드가 많이 발생한다.

비선점 스케줄링은 선점 스케줄링과 반대이다.
선점 스케줄링은 I/O 요청, I/O 응답, Interrupt 발생, 작업 완료 등의 상황에서 스케줄링이 일어날 수 있다. (선점이 일어남)
비선점 스케줄링의 경우 프로세스가 작업이 완료되는 시점에만 스케줄링이 일어난다.

### SRTF (Shortest Remaining Time First) 스케줄링

SJF의 선점형 방식이다. 먼저 온 프로세스가 CPU를 할당받고 있더라도 남은 처리 시간이 뒤에 온 프로세스의 처리 시간보다 길면 CPU를 빼앗긴다. (짧은 프로세스 먼저)

하지만 기본적으로 선점형 방식이기 때문에 잦은 문맥 교환이 일어나고 그에 따른 오버헤드가 커진다. 그리고 기아 현상이 더 심각하게 발생할 수 있다.(처리시간이 긴 프로세스는 할당 못받음)


### 라운드 로빈 (Round-Robin) 스케줄링

라운드 로빈은 프로세스에게 각각 동일한 CPU 할당 시간(타임 슬라이스, quantum)을 부여해서 이 시간 동안만 CPU를 이용하게 한다. 만약 할당 시간동안 처리를 다 하지 못하면 CPU를 빼앗고 다음 프로세스에게 넘긴다. 빼앗긴 프로세스는 준비 큐의 맨 뒤로 간다. 따라서 선점형 방식이다.
따로 CPU 처리 시간을 계산하지 않아도 돼서 선점형 방식의 가장 단순하고 대표적인 방법이다. 우선 순위도 없기 때문에 매우 공평하다.

모든 프로세스가 최초 응답 시간을 빠르게 보장받을 수 있다는 큰 장점을 가지게 된다. 자연히 콘보이 효과가 줄어든다.

> 콘보이 효과란, CPU를 매우 오래 사용하는 프로세스가 도착하게 되면, 다른 프로세스가 CPU를 사용하는데 기다리는 대기 시간이 매우 커지는 현상을 말한다.

라운드 로빈에서 가장 중요한 부분은 타임 슬라이스 크기 결정이다.

타임슬라이크가 크면 처리 시간이 긴 프로세스에 의해 CPU 효율성이 떨어질 수 있다. 너무 크면 FCFS와 다를게 없다.
반대로 작을 경우 여러 프로그램이 동시에 실행되는 효과를 볼 수 있지만, 너무 작으면 잦은 문맥 교환으로 오버헤드가 상당히 커진다.

### 다단계 큐(Multi-level Queue) 스케줄링

다단계 큐 스케줄링은 우선순위에 따라 준비 큐를 여러 개 사용하는 방식이다. 당연히 우선 순위가 높은 큐에 먼저 CPU가 할당되어 큐에 속한 모든 프로세스가 처리돼야 다음 우선순위 큐가 실행될 수 있다. 
그리고 한 번 우선순위가 매겨져 준비 큐에 들어가면 이 우선순위는 바뀌지 않는다.

프로세스를 우선순위에 따라 그룹화하고, 각 그룹에 대해 서로 다른 스케줄링을 적용할 수 있다.


![[Pasted image 20240319011637.png]]

각 큐는 독립적인 스케줄링 알고리즘을 가질 수 있는데, 보통 전면 프로세스들이 속해있는 큐는 우선순위가 높고, 라운드 로빈 스케줄링을 사용해 타임 슬라이스를 작게한다.

후면 프로세스에는 사용자와의 상호작용이 없으므로 가장 간단한 FCFS 방식으로 처리한다. 
보통 총 CPU 시간이 전면 프로세스 처리 80%, 후면 프로세스 처리 20%가 할당된다.

**다단계 큐 알고리즘 문제**
- 기아 현상
- 공평성 문제

> [!note] 전면 프로세스, 후면 프로세스?
> - 전면 프로세스: GUI를 사용하는 운영체제에서 화면의 맨 앞에 높인 프로세스이다. 입력과 출력을 사용하는 프로세스이며, **사용자와 상호작용이 가능하여 상호작용 프로세스**라고도 한다.
> - 후면 프로세스: 사용자와의 상호작용이 없는 프로세스이다. 압축 프로그램처럼 사용자의 입력 없이 작동하기 때문에 **일괄 작업 프로세스**라고도 한다.
> - 전면 프로세스는 사용자의 요구에 즉각 반응해야 하지만 후면 프로세스는 상호작용이 없다. 따라서 전면 프로세스의 우선순위가 후면 프로세스보다 높다. 그만큼 후면 프로세스는 전면 프로세스보다 CPU를 할당받을 확률이 적다.

### 다단계 피드백 큐 스케줄링

다단계 큐 공평성 문제를 완화하기 위해 신분 하락이 가능한 알고리즘이다. 
이 알고리즘은 우선순위가 변동되기 때문에 큐 사이의 이동이 가능하다.

한 번 CPU를 할당 받은 프로세스는 우선순위가 조금 낮아진다. 따라서 더 낮은 큐로 이동하게 된다.

이를 더 보완하기 위해 우선순위가 높은 큐보다 낮은 큐에 타임 슬라이스 크기를 크게 준다.
어렵게 얻은 CPU를 더 오래 사용하게 해주기 위함이다.



## 비선점형 스케줄링(non-preemptive scheduling)

- 이미 할당된 CPU를 다른 프로세스가 강제로 빼앗아 사용할 수 없는 기법이다.
- 프로세스가 CPU를 할당받으면, 해당 프로세스가 자발적으로 CPU를 방출하거나(IO 입출력 발생할 때) 실행이 종료될 때까지 다른 프로세스가 CPU를 선점할 수 없다. 
- 필요한 Context Switching 만 일어나기 때문에 오버헤드가 상대적으로 적지만 프로세스의 배치에 따라 효율성 차이가 많이 난다.

### SJF(Shortest Jop First) 스케줄링

준비 큐에 있는 프로세스 중 실행 시간이 가장 짧은 작업부터 CPU를 할당하는 비선점형 방식이다. 늦게 도착하더라도 CPU 처리 시간이 앞에 대기중이 프로세스보다 짧으면 먼저 CPU를 할당 받을 수 있기 때문에 콘보이 효과를 완화할 수 있다.

SJF 스케줄링은 FCFS 보다 효율성이 매우 높지만, 공평성에 어긋난다.
처리 시간이 긴 프로세스의 경우 짧은 프로세스가 계속 들어오면 기아 현상이 발생할 수 있다.

### HRN(Highest Response Ratio Next) 스케줄링

긴 작업과 짧은 작업 간의 지나친 불평등을 어느정도 보완한 기법이다.
수행 시간의 길이와 대기 시간을 모두 고려해 우선순위를 정한다.
SJF 스케줄링에 Aging 기법을 합친 비선점형 알고리즘이다.

Aging은 나이를 먹는다는 의미 그대로 기아현상을 해결하기 위해 대기 시간이 길어지면 우선순위를 높여주는 방법이다.

SJF와 마찬가지로 실행 시간이 적은 프로세스의 우선순위가 높지만, 대기 시간이 너무 길어지면 실행 시간이 길더라도 CPU를 할당받을 수 있다. 하지만 공평성이 말끔히 해결되지는 않는다.

### FCFS 스케줄링 (First Come First Serve Scheduling)

CPU를 먼저 요청한 프로세스가 먼저 배정 받는 스케줄링 방법이다. (선입선출)
프로세스가 준비 큐에 도착한 순서에 따라 CPU를 할당한다.
프로세스의 CPU 처리 시간을 따로 고려하지 않기 때문에 매우 단순하고 공평한 방법이다. 하지만 CPU 처리 시간이 긴 프로세스가 앞에 올 경우 콘보이 효과가 발생한다.


### 우선순위 (Priority)

- 각 프로세스 별로 우선순위가 주어지고, 우선순위에 따라 CPU 할당
- 우선순위가 같을 경우 FCFS 적용
- 설정, 자원 상황등에 따른 우선순위를 선정해 주요/긴급 프로세스에 대한 우선처리 가능

### 기한부 (Deadline)

- 작업들이 명시된 기한 내에 완료되도록 계획한다.
- deadline은 작업의 요구사항이나 제약사항에 따라 결정된다.
- 만약 deadline을 넘어서 작업이 완료되면, 해당 작업은 tardiness(지연) 혹은 deadline miss(마감 기한 미스)로 간주되며, 이는 작업의 품질이나 서비스 수준에 부정적인 영향을 미