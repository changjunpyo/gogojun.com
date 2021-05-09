---
title: '[Java] 8. 예외처리'
date: 2021-05-06 13:05:56
category: java
thumbnail: { thumbnailSrc }
draft: false
---

## 목표

자바의 예외 처리에 대해 학습해보자

## 목차

- 자바 예외 처리 방법
- 자바가 제공하는 예외 계층 구조
- Exception과 Error의 차이
- RuntimeException과 아닌 것의 차이
- 커스텀 예외처리 방법



## 에러(Error)와 예외(Exception)

 프로그램에는 에러 또는 오류에 의해서 종료되거나 문제가 생길 수있다.

에러는 발생하는 시점을 기준으로 **컴파일 타임 에러**와 **런타임 에러**로 구분이 가능하다. 컴파일 에러는 실제 컴파일 하는 과정에서 컴파일러가 판단 가능한 에러이고, 런타임 에러는 실제 프로그램을 실행하는 도중 발생하는 에러이다.

컴파일 에러는 컴파일러에 의해 문법 철자 등의 오류를 판단한다면 런타임 에러는 어떤 방식으로 오류를 판단할까?

자바에서는 런타임 에러를 에러(error)와 예외(exception)라는 두 가지로 구분하여 대응한다.

**에러**는 스택오버플로우(StackOverFlowError), 메모리 부족(OutOfMemoryError)와 같이 하드웨어나 JVM 등의 시스템적인 문제가 발생하는 경우로 프로그램 코드로 수습 할 수 없는 심각한 오류이다. 발생하는 경우 무조건 프로그램은 비정상으로 종료된다.

**예외**의 경우는 발생하더라도 프로그램 코드에 의해서 수습이 가능한 미약한 오류이다. 예외 처리를 통해서 발생 가능한 예외에 대해서 대비를 하는 코드 작성을 통해 정상 실행 상태를 유지가 가능하다.

## 자바 예외 처리 방법

### try-catch(예외처리구문)

```java
try{
  // 예외 가능성이 있는 코드를 작성
  // 한줄이여도 괄호 생략 불가능
} catch (Exception1 e1){
  // Exception1이 발생 후 처리 로직
} catch (Exception2 e2){
  // Exception2이 발생 후 처리 로직
} catch (ExceptionN eN){
  // ExceptionN이 발생 후 처리 로직
}
```

 try 블럭에는 여러 개의 catch 블럭이 가능하며, 위에서 부터 순차적으로 확인하며 각 예외는 하나의 catch 블럭만 수행된다. Exception N은 예외 클래스를 가리키며 en은 참조 변수이다.

```java
public static void main(String[] args) {
        try {
            System.out.println(0 / 0);
        } catch (ArithmeticException e) {
            System.out.println(e.getMessage());
        } catch (NullPointerException e) {
            System.out.println(e.getMessage());
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
 }
```

> printStackTrace() : 예외 발생 당시에 호출스택에 있던 메서드 정보와 예외 메세지를 화면에 출력
>
> getMessage(): 발생한 예외클래스의 인스턴스에 저장된 메세지를 얻을 수 있다.

**try-catch 블럭의 흐름**

1. 발생한 예외와 일치하는 catch 블럭을 찾는다.
2. 일치하는 블럭이 있는 경우 catch 내부 코드를 실행하고 try-catch 블럭을 빠져나가서 다음 코드를 실행한다. 일치하는 블럭이 없는 경우 예외 처리가 되지 못한다.

- 예외가 발생하지 않은 경우 catch 블럭을 전부 통과하고 다음 문장부터 실행 된다.



### 멀티 catch블럭(JDK 1.7 ~)

- 기존에 여러 catch 블럭을 하나의 블럭으로 처리하는 방식( '|' 기호를 통해 연결 )
- 중복되는 코드를 줄여준다.
- 부모 자식 관계에 있는 클래스인 경우 컴파일에러

```java
public static void main(String[] args) {
        try {
            System.out.println(0 / 0);
        } catch (ArithmeticException | IllegalArgumentException e) {
            System.out.println(e.getMessage());
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
 }
```

```java
public static void main(String[] args) {
        try {
            System.out.println(0 / 0);
          // 아래의 경우 Excption e(부모클래스)를 넣는 것과 차이가 없으므로 컴파일러에서 에러를 발생시킨다.
        } catch (Exception | IllegalArgumentException e) {
            System.out.println(e.getMessage());
        } 
 }
```



### throw 키워드

예외를 발생시키는 방법으로 throw를 통해 예외를 던져 줄 수 있다.

```java
public static void main(String[] args) {
        try {
            Exception e = new Exception("예외 발생");
        } catch (Exception e){
          System.out.println(e.getMessage());
          e.printStackTrace();
        }
 }
```

### throws 키워드

throws 키워드를 통해서 메서드에 예외를 선언 해줄 수 있다. 여러 개의 메서드를 선언해주는 것 또한 가능하다.

```java
void exceptionMethod() throws Exception1, Exception2{
  
}
```

throws의 의미는 메서드가 선언된 예외를 던질 수도있다는 것을 명시하는 것이다. 따라서 이 메서드를 사용하는 클라이언트에게 예외처리를 할 수 있도록 알려준다.

따라서 throws 자체가 예외를 처리해주는 것이 아니라 던질 수 있는 예외를 선언하여 사용자에게 예외에 대한 책임을 맡긴다.

### finally 키워드

finally는 try-catch 구문과 함께 try-catch-finally라고도 불리며 try-catch에서 예외 발생 여부와 상관 없이 항상 실행되어야하는 구문을 포함 시킬 목적으로 사용된다. try-catch문 끝에 선택적으로 붙여서 사용할 수 있다.

```java
try{
  // 예외 가능성이 있는 코드를 작성
} catch (Exception1 e1){
  // Exception1이 발생 후 처리 로직
} finally{
  // 항상 실행되는 코드
}
```

예외가 발생하면 try-> catch -> finally 순서로 실행되며, 예외가 발생하지 않은 경우 try -> finally 순서로 실행된다.

헷갈릴 수도 있는 부분으로 finally에 있는 코드는 항상 실행된다. 즉, try-catch 블럭에서 return문을 만나 함수가 종료되어도 finally를 실행하고 종료한다는 것이다.

## 자바가 제공하는 예외 계층 구조

![image](https://gptjs409.github.io/img/2019-09-06/exception-handling-001-exception1.png)

모든 클래스의 조상인 Object를 Throwable이 상속받고, 그것이 Exception 클래스와 Error 클래스로 나뉘며,

Exception은 RuntimeException과 나머지 부분으로 구분된다.



## RuntimeException과 아닌 것의 차이 (checked vs unchecked)

위 그림을 보면 Exception에서 RuntimeException이 아닌 나머지 클래스는 `checked exception`라고 부르고, Error와 RuntimeException의 경우는 `unchecked exception`이라고 부른다. 

예외라는 것은 프로그래밍을 하는 관점에서 문제가 생길 수있는 요소로 매우 중요하다. 따라서 확인 할 수있는 예외라면 명세를 강제하도록 하는 것이다. 

하지만 Error와 Runtime exception의 경우는 확인을 한다고 해서 메서드를 사용하거나 호출 하는 입장에서 예외 처리를 통해서 문제를 해결하거나 수습하는 코드를 짜기가 어렵다. 또한 Runtime Exception은 매우 빈번이 발생하기 때문에 메서드에 명시하도록 강제하는 것은 프로그래밍의 명확성을 떨어뜨리기 쉽다. 

따라서 클라이언트 입장에서 예외 처리를 통해 문제를 잘 회복할 수 있을 것이라 예상되는 checked exception과 그렇지 않은 unchecked Exception으로 구분을 해준 것이다.

`Checked Exception`은 컴파일 시점에서 확인될 수 있는 예외로 만약 코드에서 checked Exception을 발생시킨다면, 해당 예외는 반드시 처리되거나, 해당 코드가 속한 메서드의 선언부에서 반드시 선언해주어야 하며 아닐 경우 컴파일 에러가 발생한다.

```java
public class ExceptionEx{
  public static void main(String[] args){
  	methodA();
  }
  // checked exception을 선언을 해주지 않았기 때문에 컴파일 에러
  static void methodA() { 
    throw new ClassNotFoundException();
  }
}
```

아래와 같이 수정이 필요하다.

```java
public class ExceptionEx{
  public static void main(String[] args){
  	checkedExceptionMethod();
  }
  static void checkedExceptionMethod() throws ClassNotFoundException { 
    throw new ClassNotFoundException();
  }
}
```

`Unchecked Exception` 의 경우는 아래와 같이 바로 사용이 가능하다.

```java
public class ExceptionEx{
  public static void main(String[] args){
  	uncheckedExceptionMethod();
  }
  static void uncheckedExceptionMethod() {
    throw new NullPointerException();
  }
}
```



## 커스텀 예외처리 방법

- 기존의 예외 클래스를 상속받아서 새로운 예외 클래스를 정의할 수있다.

  ```java
  class myException extends Exception {
    //에러 코드 추가
    private final int ERR_CODE;
    myException(String msg, int err_code){
      super(msg);
      ERR_CODE = err_code;
    }
    myException(String msg){
      this(msg,100);
    }
    public int getErrCode() {
      return ERR_CODE;
    }
  }
  ```

  ### 참고
  자바의 정석, 남궁 성 저