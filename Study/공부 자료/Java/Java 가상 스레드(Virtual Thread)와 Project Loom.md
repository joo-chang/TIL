# Java 가상 스레드(Virtual Thread)와 Project Loom

## 서론(개요)

Java의 가상 스레드(Virtual Thread)는 기존 운영체제(OS) 스레드와 달리 경량화된 스레드로, 수십만 개의 동시 작업을 효율적으로 처리할 수 있도록 설계된 새로운 실행 단위이다. Project Loom은 이러한 가상 스레드 등 동시성(concurrency) 모델을 혁신적으로 개선하기 위해 Java 진영에서 추진한 프로젝트로, 동시성 코드 작성의 복잡성을 크게 완화한다.

## 주요 개념

- **가상 스레드(Virtual Thread)**: OS 스레드와 분리되어 Java 런타임(JVM)에서 관리되는 매우 가벼운 스레드이다. 생성 및 스케줄링을 JVM이 처리해서, 기존 방식보다 훨씬 많은 동시 작업을 적은 리소스로 실행할 수 있다.
- **Project Loom**: 기존의 스레드 기반 동시성 모델의 한계를 극복하고, 코드 구조는 Blocking 스타일을 그대로 유지하면서 높은 확장성을 제공하는 것을 목표로 하는 Java 프로젝트이다.  
- **실무 장점**: 고성능 HTTP 서버, 대규모 트래픽 처리, 데이터베이스 접근 등 I/O 중심 애플리케이션의 동시성 구현이 단순해지고, 리액티브 프로그래밍의 복잡한 콜백 구조를 피할 수 있다.

## 실습 예제

### 기존 스레드와 가상 스레드의 차이

```java
// 기존 Java 스레드
for (int i = 0; i < 10_000; i++) {
    new Thread(() -> {
        System.out.println("Hello from Platform Thread " + Thread.currentThread());
    }).start();
}

// Java 21 이후, 가상 스레드 사용 예시
for (int i = 0; i < 10_000; i++) {
    Thread.startVirtualThread(() -> {
        System.out.println("Hello from Virtual Thread " + Thread.currentThread());
    });
}
```
- 기존 OS 스레드는 수백~수천 개 이상 생성 시 리소스 한계로 인해 성능 저하가 발생한다.
- 가상 스레드는 수만~수십만 개를 부담 없이 생성하여 동시성 확장성을 획기적으로 향상시킨다.

### 고급 구현 – 가상 스레드를 활용한 HTTP 서버

```java
import java.io.*;
import java.net.*;

public class VirtualThreadHttpServer {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(8080);

        while (true) {
            Socket client = serverSocket.accept();
            Thread.startVirtualThread(() -> handleClient(client));
        }
    }

    private static void handleClient(Socket client) {
        try (BufferedReader in = new BufferedReader(new InputStreamReader(client.getInputStream()));
             PrintWriter out = new PrintWriter(client.getOutputStream(), true)) {
            String line = in.readLine();
            // 간단한 HTTP 응답
            out.println("HTTP/1.1 200 OK");
            out.println("Content-Type: text/plain");
            out.println();
            out.println("Hello, Virtual Thread!");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
- 각 요청마다 새로운 가상 스레드를 할당함으로써, 수많은 동시 접속 처리에도 성능 저하 없이 동작한다.
- 개발자는 전통적인 blocking 코드 스타일을 그대로 활용할 수 있어 비동기·콜백 지옥을 피할 수 있다.

## 정리/요약

Java 가상 스레드는 기존 OS 기반 Thread의 한계를 극복하고, 대규모 동시성 처리 성능을 획기적으로 개선한다. Project Loom은 개발자에게 친숙한 코드 스타일을 유지하면서, 리액티브 프로그래밍의 복잡성을 크게 줄이는 최신 Java의 동시성 패러다임이다. 대규모 트래픽 애플리케이션, HTTP 서버, 데이터 처리 등에서 실질적 이점을 제공한다.

## 참고 자료

- [Project Loom 공식 문서](https://openjdk.org/projects/loom/)
- [Java 21의 Virtual Threads 소개 - Baeldung](https://www.baeldung.com/java-virtual-threads)
- [The State of Loom in Java 21](https://www.infoq.com/articles/java-virtual-threads-production/)

