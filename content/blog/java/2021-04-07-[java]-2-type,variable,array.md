---
title: "[Java] 2. 타입, 변수, 배열"
date: 2021-04-07 23:04:64
category: java
thumbnail: { thumbnailSrc }
draft: false
---


Data의 타입이라는 것은 해당 자료가 메모리에 어떤 방식으로 저장되는지 명시적으로 알려주는 것이다. 
크게 2가지의 타입으로 **기본형(Primitive)** 타입과 **참조형(Reference)** 타입이 존재한다.

## 기본형 타입(Primitive type)

- 8가지의 기본형 타입이 존재한다.
- `null` 값이 존재하지 않는다. 기본 값이 할당 된다 -> 언제 기본 값이 할당될까?
- 실제 연산이 일어나게 되는 경우 전부 기본형 타입으로 연산 된다.
  

  

  | 기본형 분류| 타입       | 바이트 | 범위                                       | 기본값   |
  |:--------------------------:|:----------:|:-----------------:|:---------------------------:|:-----------:|
  | **논리형**| boolean    | 1byte | true, false                                | false    |
  | **정수형**| byte       | 1byte | -128 ~ 127                                 | 0        |
  |         | short      | 2byte | - 2^15 ~ (2^15) -1                         | 0        |
  |         | **int**    | 4byte | - 2^31 ~ (2^31) -1                         | 0        |
  |         | long       | 8byte | -2^63 ~ (2^63)-1                           | 0        |
  | **실수형**| float      | 4byte |3.4 X 10^-38 ~ 3.4 X 10^38 근사값   | 0.0F    |
  |         | **double** | 8byte |1.7 X 10^-308 ~ 1.7 X 10^308 근사값 | 0.0     |
  |**문자형**| char       | 2byte |0 ~ 2^16 -1                                |\u0000'|

  

## 참조형 타입(Reference type)

- 자바에서 기본형을 제외한 모든 타입은 참조형 타입이다 (`Class, Interface, Array, Enum`)
- 항상 4 byte의 크기를 가지며 해당 객체의 해시코드 값 or null 값을 가진다.
- 선언시 기본 값으로 null로 초기화 된다.
  - null을 가리키는 레퍼런스의 객체에 접근하여 작업을 하려하면 런타임 에러 (NullPointerException)이 발생한다.

- 참조 타입이 참조하고 있는 데이터는 heap 영역에서 관리되며 참조되지 않는 값은 GC의 대상이 된다.
- 참조형 타입 변수는 heap 영역에 메모리가 할당되지 않는다(스택에 생성된다)

```java
Person person = new Person();
// 여기서 person은 참조형 타입 변수 -> 스택에 생성
// new Person()으로 생성된 객체는 heap에 생성
```

참고: 자바의 참조형 타입은 포인터와 어떻게 다를까?  [Does Java have pointers?](https://stackoverflow.com/questions/2629357/does-java-have-pointers)



## 리터럴(Literal)

리터럴은 변하지 않는 값 그 자체를 의미한다. 여기서 상수(Constant)와 비슷하다고 생각할 수 있다. 하지만 완전히 다르다. 

아래를 보자

```java
int a = 1;
final char B = 'c';
```

여기서 리터럴은 `1` 과 '`c`'라고 할 수 있고  `B`는 상수이다.
즉, **상수**라는 것은 변하지 않는 변수이고 **리터럴**은 메모리 안의 값을 의미한다. 

기본형 타입이 리터럴이 된다는 것은 알았다. 하지만 인스턴스(객체)도 리터럴이 될 수 있을까?
데이터가 변하지 않도록 설계하여 만든 클래스로 불변 클래스(immutable class)가 있다. 예를 들면, String과 같은 클래스는 불변 클래스이다. 따라서 "xyz"와 같은 문자열은 우리는 객체 리터럴이라고 부른다.

## 변수 선언 및 초기화 (Declaration , Initialization)

### 변수 선언(Declaration)

변수 선언이라는 것은 코드로 변수가 있음을 알려주는 것이다. 어떤 변수가 어떤 타입으로 존재한다라고 명명하는 것이다.

### 변수 초기화(Initialization)

변수가 어떻게 명명되는지 선언되었다면, 변수에 초기에 어떤 값이 들어가 있는지 설정하는 것이다.
전역변수와 static 변수의 경우 기본 값이 설정되지만, 지역변수의 경우 반드시 초기화 되어야한다(컴파일 에러 발생)

```java
// 변수 선언
int x;
Person p;
// 변수 초기화
x= 1;
p = new Person();

// 변수 선언 & 초기화
int x = 1;
Person p = new Person();
```



## 변수의 스코프 및 라이프타임(Scope & Lifetime)

### 변수의 스코프

프로그램에서 사용되는 변수는 사용 가능한 범위를 가지는데 그것을 변수의 스코프라고 부른다.

#### 변수가 선언된 블럭({})안이 그 변수의 스코프이다.

```java
Public class Main{
	int global = 1; // 
  
  public void test(int val){
    int local = 2;
    System.out.println(globalScope);
    System.out.println(localScpe);
    System.out.println(value);
  }
}
```

- 클래스 속성으로 선언된 `global` 변수(인스턴스 변수)는 클래스 전체에서 사용 가능하다.
- 매개 변수로 선언된 val은 블럭 바깥에 있긴하지만, 메서드 내부에서 사용 가능하다.
- 메서드 블럭안에서 선언된 `local` 변수는 메서드 블럭에서 사용 가능하다.

#### Static

- 정적으로 선언된 타입은 클래스가 인스턴스화 되지 않아도 사용가능하다.
- 전체적으로 메모리에 저장하는 공간이 하나만 생성된다. 인스턴스가 여러 개여도 같은 객체를 가리킨다. (메모리 힙영역이 아닌 메서드영역에 존재한다)
- static한 메서드에서는 static 필드가 아닌 클래스 내부 필드를 사용할 수 없다 -> 인스턴스화 되지 않아도 사용 가능 해야하기 때문

### 변수의 라이프 타임

#### 로컬 변수(메서드 내부 파라미터 및 내부 변수)

- 메서드 종료시 스택에서 사라 진다.

#### 인스턴스 변수

- 객체가 GC에 의해 처리되기 전까지

#### 정적 변수

- 클래스가 메모리에 로드 되는 동안

## 타입 변환, 캐스팅, 타입 프로모션

### 타입 변환의 필요성

연산을 수행하고자 할 때 같은 타입의 연산만 가능하다. 따라서 연산을 수행하기 전에 같은 타입으로 만들어 주어야 한다.

아래의 그림의 화살표 방향으로 **자동 형변환(프로모션)**이 일어난다.

![참조: https://league-cat.tistory.com/412 ](https://blog.kakaocdn.net/dn/cZ8YEL/btqNYvYLYXt/SKJ89Ikcs2lT4kbq5XPlD0/img.png)

[참조: https://league-cat.tistory.com/412]

### 자동 형변환(Type promotion)

자동 형변환이라는 것은 작은 타입에서 큰 타입으로 할당이 이루어 지는 경우인데, 여기서 큰 타입이라는 것은 위 그림에서 보는 표현 할 수 있는 범위가 더 넓은 경우이다. 따라서 자동으로 값을 변환하여 넣어준다.

```java
short a = 12345;
int b = a;
System.out.println(b); // 예상데로 12345 출력
```



### 타입 캐스팅(Type casting)

타입 캐스팅이라는 것은 명시적으로 즉, 강제적으로 형변환을 하도록 하는 것이다. 이것은 큰 타입에서 작은 타입으로 변환을 하는 경우 필요하다.  이 경우 작은 것이 표현하지 못하는 수를 변환하는 경우 예상치 못한 값이 나올 수 있다.

아래의 예제를 보자.

```java
// 큰 수가 작은 수에 범위 안인 경우
long a =1;
short b = (short) a;
System.out.println(b); // 1이 출력
// 큰 수가 작은 수 범위 밖인 경우
long a = 123456789;
short b = (short) a;
System.out.println(b); // -13035이 출력
```



## 1D, 2D 배열 선언

### 1D 배열 선언, 생성,  초기화

```java
// 선언
int[] array; // type[] var;
int array[]; // type var[];

// 생성
int []array = new int[5]; // 선언 & 생성

// 초기화
int[] array = {1,2,3,4,5};
int[] array = new int[]{1,2,3,4,5}

// ERROR
int[] array;
array = {1,2,3,4,5};
```



### 2D 배열 선언, 생성, 초기화

```java
//선언 방식
int[][] arr; // type[][] var;
int arr[][]; // type var[][];
int[] arr[]; // type[] var[];
// 생성
int [][]arr = new int[5][3];
// 가변 배열 생성
int [][]arr = new int[5][];
arr[0] = new int[3];
arr[1] = new int[2];
...
arr[4] = new int[5];
int [][] score = {
  {1,2,3,4,5},
  {1,2,3,4},
  {2,4,5}
};

```



## 타입 추론 

Java 10버전 이후 `var` 이라는 키워드가 추가되었는데, Local variable Type Inference를 하는 키워드이다. 즉, 코드 작성 당시는 타입이 정해지지 않았지만 컴파일러의 타입 추론에 의하여 타입을 유추해낸다.

`var`의 경우 반드시 타입 선언과 초기화를 해주어야 한다. 컴파일러가 오른쪽 초기화에 의해서 타입을 유추하기 때문이다.

Java 7에는 다이아몬드 연산자가 추가되었는데, 이것과 `var`를 같이 사용하는 경우 컴파일 에러가 발생한다. 

```java
var array = new ArrayList<>(); // 둘 다 추론을 해야하는데 정보가 없다...
```

- 사용에 따라서 어떤 타입인지 명확히 알 수 있다면 괜찮다.
- 하지만 `var`를 남발하면 가독성이 떨어질 수 있기에 주의 해야한다.

