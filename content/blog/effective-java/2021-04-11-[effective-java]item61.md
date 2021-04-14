---
title: '[Effective-Java] Item61. 박싱된 기본 타입보다는 기본 타입을 사용하라'
date: 2021-04-11 16:04:51
category: effective-java
thumbnail: { thumbnailSrc }
draft: false
---

# 박싱된 기본 타입보다는 기본 타입을 사용하라



## 자바의 기본 데이터 타입 

**기본형(Primitive)** / **참조형(Reference)**

각각의 기본 타입에는 그에 대응하는 참조형(박싱형 기본타입)을 같고 있다. ex )`int` -> `Interger` , `double` -> `Double`

### 오토 박싱 , 오토 언박싱

JDK 1.5 이후에 나온 기능으로 컴파일러에 의해서 자동으로 박싱형 타입을 기본형 타입으로 바꿔 주는 것을 오토언박싱, 그 반대를 박싱이라고 한다. 언제 오토 박싱, 언박싱을 할까?

```java
List<Integer> l = new ArrayList<>();
l.add(10); // 오토 박싱
l.add(20); // 오토 박싱

int i = l.get(0); // 오토 언박싱
```



## 기본 타입과 박싱형 기본 타입의 차이점

1. 기본 타입은 단지 값만을 가지지만 박싱형 타입은 **식별성(identity)**이라는 속성을 가진다. 이것은 참조형 타입의 특징으로 인스턴스가 가지고 있는 값이 같더라도 다르다고 식별 할 수 있다.
2. 기본 타입은 언제나 유효한 값을 가지지만, 박싱형 기본 타입은 **`null` 값**을 가진다.
3. 기본 타입은 박싱형 기본 타입보다 **메모리와 시간 측면에서 효율적**이다.



위의 세가지의 다른 점을 이해하지 못하고 사용한다면 문제가 생긴다. 그 문제는 에러일 수 있고 성능면에서 엄청나게 손헤를 보고 있을 수 있다. 

대부분의 문제는 오토 박싱과 언박싱이 일어나는 과정을 인지하지 못하고 사용하는 경우 생긴다. 아래의 경우를 보자.

#### 1. 식별성

```java
public static void main(String[] args) {
       Integer i = new Integer(2);
       Integer j = new Integer(2);
        System.out.println(i == j); // false
        System.out.println(i <= j); // true
        System.out.println(i >= j); // true
}
```

위의 코드를 보면 당연히 참조형 타입은 같은 값을 가지고 있더라도 같은 객체를 참조하는 것이 아니기 때문에 다르다고 판단한다. 하지만 아래의 비교문을 보자 비교문의 객체의 비교를 하기 위해 자동으로 박싱타입을 기본형으로 바꿔서 비교를 하기 때문에 정상적으로 작동한다. 

따라서 위와 같이 박싱형 타입을 쓰는 경우 주의가 필요하다.

#### 2. null 

```java
public class NullProblem{
  static Integer i;
  
  public static void main(String[] args){
    if (i == 0){
      System.out.println("????");
    }
    else{
      System.out.println("haha")
    }
  }
}
```

위에서 나오는 결과는 무엇일까?

`i` 의 경우 초기화 되는데 참조형의 경우는 `null` 로 초기화 된다. 따라서 `i == 0`을 하면서 i를 참조하게 되는데 기본 형과 비교문이 있는 경우 오토 언박싱을 하게 된다. 그러면서 `null` 객체를 참조하게 되고 `NullPointerException`을 마주하게 된다. 이런 경우 해결 방법은 단순히 `int` 타입으로 바꿔주면 된다.

### 3. 성능

```java
public static void main(String[] args){
Integer sum = 0;
  for (int i=0; i<100000; i++){
    sum += i; // 오토 박싱, 언박싱이 계속 일어남!
  }
  System.out.println(sum);
}
```

위의 예제를 보면 sum의 타입은  박싱 타입으로 선언하여 반복문 안에서 계속 오토 언박싱과 박싱이 계속 일어난다. 따라서 성능 상에서 느껴질 정도로 속도가 느려지게 된다.



## 박싱타입을 써야하는 경우

1. **컬렉션의 원소, 키, 값**으로 쓰는 경우

   자바에서 매개변수화 타입이나 매개변수화 메서드의 타입 매개변수로는 박싱된 기본 타입을 써야한다.

   `ArrayList<int>`는 불가능 하며, `ArraList<Integer>`라고 작성하여야 한다.

2. **리플렉션**을 통해 메서드를 호출 하는 경우

    ##### 리플렉션이란?
   구체적인 클래스 타입을 알지 못해도, 그 클래스의 메소드, 타입, 변수들에 접근할 수 있도록 해주는 자바 API

   프레임워크나, 라이브러리에서 많이 사용된다. (Spring, intelliJ)




#### 참조
*1. Effective java 3/e , by Joshua Bloch*