---
title: '[Java] 9. 멀티쓰레드 프로그래밍'
date: 2021-05-10 20:05:68
category: java
thumbnail: { thumbnailSrc }
draft: false
---

## 목표 

자바 멀티쓰레드 프로그래밍에 대해 학습하세요

기본적으로 쓰레드를 이해하고 사용할 수있는 수준으로 예제와 내용을 학습한다.

## Thread

먼저 쓰레드에 대해서 간단히 이해해보자

- 쓰레드는 프로그램에서 하나의 실행 흐름을 의미
- JVM에서 병렬적으로 작동하는 여러 개의 스레드 실행을 허용한다
- 스레드는 우선 순위를 가지고 동작한다. 우선순위가 높은 스레드는 낮은 쓰레드보다 먼저 실행된다.

#### 멀티 쓰레드의 장점 

- 자원을 효율적으로 사용 가능
- 작업을 분리하여 코드가 간결

#### 멀티 쓰레드의 단점

- 동기화를 주의하여 해야한다
- 교착 상태(데드락)가 발생하지 않도록 주의해야 한다



## Thread 클래스와 Runnable 인터페이스

자바에서는 쓰레드를 생성하는 방법으로는 두가지가 있다.

### Runnable 인터페이스

run() 메서드만을 가지고 있는 인터페이스이다.

```java
public interface Runnable {
  public abstract void run();
}
```

### Thread 클래스

Thread 클래스는 Runnable 인터페이스를 구현한 클래스이므로 어떤 것을 적용하느냐의 차이이다.

Runnable 과 Thread 모두 java.lang 패키지에 포함 되어있다.

```java
package java.lang;

class Thread implements Runnable {
  private static native void registerNatives();
  static {
    registerNatives();
  }
  ...
}
```

Thread에 구현되어있는 일부 메서드를 살펴보자

- sleep() - 시스템 타이머 및 스케줄러의 정밀도에 따라 현재 실행중인 스레드를 지정된 밀리초동안 sleep 시킨다.
- yield() - 실행 중 자신에게 주어진 실행시간을 다른 쓰레드에게 양보하고 자신은 실행 대기 상태로 돌입
- start()- 쓰레드를 실행하기 위해서는 start 메서드를 통해 해당 쓰레드를 호출, 즉 쓰레드의 스택을 만들고 run 메서드 실행
- run()- 쓰레드에 호출에 의해서 실행되는 메서드
- interrupt()- 슬립 상태나 join()에 의해 일시정시 상태의 쓰레드를 꺠워 실행대기 상태로 만듬(InterruptException 발생)
- join()- 지정된 시간동안 쓰레드가 실행되도록 한다. join을 호출한 쓰레드는 그동안 일시정지가 된다. 지정 시간이 지나거나 작업 종료시 호출한 쓰레드는 다시 돌아와서 실행을 한다.

Thread 클래스가 다른 클래스를 확장해서 사용해야하는 경우는 Runnable 인터페이스를 통해 구현하며, 그렇지 않은 경우 Thread 클래스를 사용하는 것이 편하다.

## Thread의 우선순위

작업의 중요도에 따라서 쓰레드의 우선순위를 다르게 해서 특정 쓰레드가 더 많은 작업시간을 갖도록 할 수 있다.

```java
void setPriority(int newPriority) // 쓰레드의 우선순위를 지정한 값으로 변경한다.
int getPriority() // 쓰레드의 우선순위를 반환한다.
  
public static final int MAX_PRIORITY = 10 // 최대 우선 순위
public static final int MIN_PRIORITY = 1 // 최소 우선 순위
public static final int NORM_PRIORITY = 1 // 보통 우선 순위
```

## 쓰레드 그룹

- 서로 관련된 쓰레드를 그룹으로 묶어서 다루기 위한 것
- 모든 쓰레드는 반드시 하나의 쓰레드 그룹에 포함되어 있어야 한다
- 쓰레드 그룹을 지정하지 않고 생성한 쓰레드는 main 쓰레드 그룹에 속한다
- 자신을 생성한 쓰레드의 그룹과 우선순위를 상속 받는다.

#### 메인 쓰레드 

main() 메서드를 실행하는 쓰레드이다. 모든 자바 어플리케이션은 메인쓰레드가 메인 메서드를 호출하면서 프로그램을 시작한다.

#### 데몬 쓰레드

- 일반 쓰레드의 작업을 돕는 보조적인 역할을 수행
- 일반 쓰레드가 모두 종료되면 자동적으로 종료된다.
- 가비지 컬렉터, 자동저장, 화면자동갱신 등에 사용된다.
- 무한루프와 조건문을 이용해서 실행 후 대기하다가 특정조건이 만족되면 작업을 수행하고 다시 대기하도록 작성

```java
boolean isDaemon() // 쓰레드가 데몬쓰레드인지 확인
void setDaemon(boolean on) 
// 쓰레드를 데몬쓰레드로 혹은 사용자 쓰레드로 변경 
// start()를 호출하기 전에 반드시 실행되어야한다.  									
```



## 쓰레드의 상태 

자바의 Enum 타입으로 Thread.state가 존재

- new
  - 쓰레드가 아직 시작되지 않은 상태
- runnable
  - JVM에서 실행되고 있는 상태
- blocked
  - 쓰레드가 블락되어 모니터락을 얻기를 기다리는 상태
- waiting
  - 따른 쓰레드가 특정한 작업을 마치기를 기다리는 상태
- timed-waiting
  - 따른 쓰레드가 특정한 시간동안 작업을 수행하도록 기다리는 상태
- terminated
  - 쓰레드가 종료된 상태



![threadLifeCycle](/Users/junpyojang/Desktop/threadLifeCycle.jpeg)

(Reference: https://www.geeksforgeeks.org/lifecycle-and-states-of-a-thread-in-java/)





## 쓰레드 동기화

#### synchronized 키워드

한 번에 하나의 쓰레드만 객체에 접근할 수있도록 객체에 락을 걸어서 데이터의 일관성을 유지하는 것

```java
//1. 특정한 객체에 락을 걸고자 할 때
 synchronized (객체의 참조변수){
   // 작업
 }

//2. 메서드에 락을 걸고자 할 때
public synchronized void calSum(){
  //...
}

```

```java
class Account {
  int balance = 1000;
  
  public void withdraw(int money){
    if (balance >= money){
      try{ Thread.sleep(1000);} catch(Exception e){}
      balance -= money;
    }
  }
}
  
  class RunnableEx implements Runnable{
    Account acc = new Account();
    
    public void run() {
      while(acc.balance > 0){
        int money = (int)(Math.random() * 3 +1) *100;
        acc.withdraw(money);
        System.out.println("balacne: " + acc.balance);
      }
    }
  }
```

Synchronized가 있는 경우와 없는 경우 나오는 결과가 다르다. 붙이지 않은 경우 음수가 나올 수 있다.

#### Object 클래스에 정의된 동기화 메서드 

**wait()**: 객체의 lock을 풀고 해당 객체의 쓰레드를 waiting pool에 넣는다.

**notify()**: waiting pool에서 대기중인 쓰레드 중의 하나를 깨운다.

**notifyAll()**: waiting pool에서 대기중인 모든 쓰레드를 깨운다. 

```java
class Account {
  int balance = 1000;
  
  public synchronized void withdraw(int money){
    if (balance >= money){
      try{ wait(); } catch(Exception e){}
      balance -= money;
    }
  }
  public synchronized void deposit(int money){
    balance += money;
    notify();
  }
}
```



## 데드락

프로세스가 자원을 얻지 못해서 다음 처리를 하지 못하는 상태로, 다음으로 진행을 하지 못하는 상태. 동기화 처리 과정에서 발생하게 된다.

#### 데드락의 발생 조건

1. **상호 배제(Mutual exclusive)**: 자원은 한 번에 한 프로세스만이 사용할 수 있어야 한다.
2. **점유 대기(Hold and wait)**: 최소한 하나의 자원을 점유하고 있으면서 다른 프로세스에 할당되어 사용하고 있는 자원을 추가로 점유하기 위해 대기하는 프로세스가 있어야 한다.
3. **비선점(Non preemptive)**: 다른 프로세스에 할당된 자원을 작업이 끝날 때 까지 강제로 회수할 수 없다. 
4. **순환 대기(Circular wait)**: 여러 개의 프로세스들이 각각 원의 형태로 다른 프로세스의 자원을 대기있는 형태여야 한다.

예방/회피 방법과탐지 및 회복 방법이 존재한다.