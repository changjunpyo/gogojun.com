---
title: '[Java] 13. 제네릭'
date: 2021-05-22 21:05:27
category: java
thumbnail: { thumbnailSrc }
draft: false
---

### 목표

자바의 제네릭에 대해 학습하자

#### 

### 목차

- 제네릭 사용법
- 제네릭 메서드 만들기
- 제네릭의 주요 개념(바운디드 타입, 와일드 카드)
- Erasure



## 제네릭이란

제네릭의 사용법과 주요 개념들을 설명하기 앞서 제네릭 대해서 이해 해보자.

[오라클의 공식 문서](https://docs.oracle.com/javase/tutorial/java/generics/why.html) 에서는 왜 제네릭을 사용하는지 설명하고 있는데, 요약하여 살펴보면 

제네릭은 클래스, 인터페이스, 메서드를 정의하는데 있어서 **타입**을 **파라미터화** 하여서 사용할 수있게 해주며, 제네릭은 타입 파라미터를 사용함으로 인해서 **동일한 코드를 재사용해서 여러 입력(타입)을 사용하는 방법을 제공**해준다.

제네릭을 사용함으로 얻게되는 이점으로 컴파일 시점에 강한 타입체크, 타입 캐스팅의 제거, 프로그래머가 다양한 타입에 대해 제네릭한 알고리즘을 작성할 수 있게하는 점이 있다.

즉, 한마디로 요약하면 제네릭은 **불필요한 코드의 중복을 막고 재사용성을 늘리기 위해** 사용한다.

아직까지는 감이 안올 수 있다. 실제 예제 코드를 보며 제네릭에 대해서 사용법을 자세하게 알아보도록 하자

## 제네릭 사용법

**제네릭 클래스**는 아래와 같은 형식으로 정의한다.

```java
class name<T1, T2, T3, ..., Tn> {/* */}
```

일반적인 클래스의 정의에 추가적으로 클래스이름 뒤에 `<>` 가 오고, 그 내부에 타입 매개변수(타입 변수)들을 적어 준다.

타입 매개 변수들의 이름은 임의로 어떤 것이든 정의하여 사용이 가능하지만 일반적으로 가독성을 위해 [네이밍 컨벤션](https://gogojun.com/effective-java/2021-04-04-[effective-java]item68/#%ED%83%80%EC%9E%85-%EB%A7%A4%EA%B0%9C-%EB%B3%80%EC%88%98)이 존재한다. 

```java
public class GenericExample<T> {
    private T value;
		GerericExample(T value){
      this.value = value;
    }
    public void setValue(T value){ this.value = value;}
    public T getValue(){ return this.value;}
}
```

```java
public static void main(String[] args) {
  			//제네릭 클래스 인스턴스화 및 레퍼런스 변수 할당
  			GenericExample<Integer> genericExample1 = new GenericExample<Integer>(1);
  			GenericExample<String> genericExample2 = new GenericExample<String>("abc");
  			
  			// jdk1.7 이후 다이아몬드를 통해 타입 추론하여 컴파일 단계에서 알아서 넣어주는 방식
        GenericExample<Integer> genericExample3 = new GenericExample<>(1);
               
    }
```



### Raw Types(로 타입)

위의 GenericExample<T>를 생성하기 위해 아래와 같이 사용하였다.

```java
GenericExample<Integer> genericExample = new GenericExample<>();
```

 하지만 아래처럼 타입 인자를 빼고 생성하게 되면  GenericExample<T>의 raw type을 만들 수 있다.

```java
GenericExample rawGenericExample = new GenericExample();
```

Raw Types은 무엇일까? 왜 이런 타입이 존재하는 걸까?

그러기 위해서는 과거 이야기를 잠깐 해야한다.

자바에서 JDK 1.5 이후 제네릭이 나오게 되었다. 제네릭이 없는 이전의 버전에서는 raw types 이용하였고(`Collections` 클래스의 코드들에는 raw types이 존재한다) 이것은 제네릭과 비슷하게 행동할 수 있다(Object로 캐스팅하여). 자바에서는 하위 호환성 위해서 파라미터 타입에 raw type을 할당하는 것을 허용하였다.

하지만 raw types은 타입 체크를 하지 않고 불안정한 코드의 문제를 런타임으로 미루는 것이 된다. 따라서 **사용하지 말아야 한다.**

실제로 Type Erasure를 통해 바이트코드에서는 제네릭 클래스와 raw type은 같은 코드로 변환 된다. Type Erasue에서 자세히 알아보자.

#### 제네릭 메서드

메서드도 제네릭으로 정의하여 타입 파라미터를 사용하는 방식이 가능하다. static 메서드와 non-static 메서드 모두 사용가능하다. 하지만 제네릭 클래스와는 조금은 방법이 다르다. 

아래 실제 Util 클래스에서 compare를 구현한 메서드 코드를 보자.

```java
public class Util {
    public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
               p1.getValue().equals(p2.getValue());
    }
}

public class Pair<K, V> {

    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public void setKey(K key) { this.key = key; }
    public void setValue(V value) { this.value = value; }
    public K getKey()   { return key; }
    public V getValue() { return value; }
}
```

위 `compare` 코드를 보면 `<K, V>`를 제네릭 클래스와 달리 리턴 타입 앞에 적어 준다.(return 타입으로 타입 파라미터가 올 수도 있기 때문에 미리 컴파일러에게 알리기 위함이라고 추측이 가능하다)



## Bounded Type Parameters(바운디드 타입파라미터) 

바운디드 타입 파라미터는 제네릭 알고리즘을 구현하는데 있어서 중요한 기능중 하나이다. 

바운디드 타입 파라미터가 필요한 상황을 알기 위한 예시를 보자.

위의 `GenericExample`을 타입으로 받는 새로운 메서드를 생각해보자

```java
public static <T> int addInteger(GenericExample<T> ge){
  T t = ge.getValue();
  return (Integer)t + 4;
}
```

다음과 같은 메서드가 있고 아래처럼 사용했다고 하면

```java
GenericExample<String> genericExample = new GenericExample<String>("abc");

addInteger(genericExample); // 컴파일에러가 아닌 런타임 에러가 발생한다.
```

String 타임이 전달되면 아래의 런타임에 문제가 발생하게 된다.

```bash
Exception in thread "main" java.lang.ClassCastException: class java.lang.String cannot be cast to class java.lang.Integer (java.lang.String and java.lang.Integer are in module java.base of loader 'bootstrap')
	at GenericExample.addInteger(GenericExample.java:10)
	at GenericExample.main(GenericExample.java:14)
```



제네릭을 사용해서 컴파일 타임에 타입 체크도 하고, 코드 재사용성도 좋아졌지만 이런 식으로 런타임의 문제를 겪는 것은 문제가 있다.

따라서 바운디드 타입 파라미터를 통해 파라미터 타입을 특정 타입으로 제한하여 이런 문제를 해결할 수있다.

```java
public static <T extends Integer> int addInteger(GenericExample<T> ge){
  T t = ge.getValue();
  // 타입 캐스팅을 할 필요가 없어짐
  return t + 4;
}
```

위와 같이 메서드에 들어올 수 있는 타입 파라미터를 `Integer`로 제한함으로 컴파일 타임에 위와 같은 문제를 생기지 않도록 제한 해줄 수 있다.

추가적으로 아래처럼 여러 조건을 전부 만족시키는 인터섹션을 통한 바운디드 타입을 지정할 수 있다.
```java
public static <T extends Integer & Comparable & Serializable> int addInteger(GenericExample<T> ge){
  T t = ge.getValue();
  // 타입 캐스팅을 할 필요가 없어짐
  return t + 4;
}
```

### 제네릭과 상속
바운디드 타입과 관련해서 제네릭에서 상속 관계를 잘못 생각할 수 있다. 

제네릭의 타입 파라미터가 상속 관계를 가지고 있다고 해서 제네릭 클래스끼리 상속관계를 가지고 있지 않다.

예를 들면, Integer 클래스는 Number 클래스를 상속 받는다. 하지만 List<Integer>는 List<Number>와 상속 관계에 있지 않다는 점이다. 아래 예시를 보자.

```java

Number n = 10;
Integer i = n; // OK, 상속 관계

List<Number> ln = new ArrayList<>();
List<Integer> li = ln // compile time error, 상속관계가 아님
```

하지만 다르게 같은 타입 파라미터를 가지고 있고 클래스(인터페이스)끼리 상속관계를 가지고 있다면 제네릭 또한 서브 타입 관계 또한 유지된다. 

예를 들어, Collection 타입을 List가 상속 받고 그 List를 ArrayList가 구현하는 관계에 있다고 하면
List<Integer>는 Collection<Integer>를 상속받고, ArrayList<Integer>를 List<Integer>가 구현하는 관계 또한 유지 된다. 

## Wildcard(와일드 카드)

제네릭 코드에서 물음표 `?` 는 와일드 카드라고 부른며 정해지지 않은 타입을 나타낸다. 

와일드 카드는 다양한 상황에서 사용된다. 타입 파라미터, 필드, 지역변수, 리턴값으로 사용된다.

하지만 제네릭 메서드를 호출할 때 타입 인자로 넘기거나 제네릭 클래스를 생성하는 경우는 할 수 없다.

와일드 카드를 통해 타입을 제한하는 형태는 보통 세가지 형태로 사용된다

- **<?>** : 모든 종류의 클래스나 인터페이스 사용 가능
- **<? extends X>**: X 또는 이 X의 하위타입만 사용가능 (Upper bounded wildcard라고 부른다)
- **<? super Y>**: Y 또는 이 Y의 상위타입만 사용 가능 (Lower bounded wildcard라고 부른다)



예시의 상황을 위해 클래스끼리 다음과 같은 관계를 가지고 있다고 해보자

```java
class GrandParent {}
class ParentA extends GrandParent{}
class ParentB extends GrandParent{}
class ChildB extends ParentB{}

public class GenericExample2 {
    public static void main(String[] args) {
       // 모든 타입 가능
        List<?> wildcardTest = Arrays.asList(
          new GrandParent(), 
          new ParentA(),
          new ParentB(),
          new ChildB())
        // ParentB의 하위 타입만 가능
        List<? extends ParerntB> wildcardExtendsTest= Arrays.asList(
          new ParentB(),
          new ChildB())
          
       // ParentA의 상위 타입만 가능
       List<? extends ParerntB> wildcardExtendsTest= Arrays.asList(
          new GrandParent(),
          new ParentA())   
         
    }
}
```

위와 같이 사용이 가능하다. 만약 타입 제한이 위배 되는 경우 다음과 같이 오류 메세지를 보여주며 에러가 발생한다.

## Type Erasure

제네릭이 실제로 컴파일러에 의해서 어떤 식으로 변환될까? 

컴파일러는 모든 제네릭 타입의 타입 파라미터를 Bounded 되어있는 경우 바운드된 클래스로 넣어주고, 모든 것이 허용되는 경우는 `Object` 타입을 넣어 준다.

자 그렇다면, 항상 List<int>를 사용할 수 없는가에 대한 해답이 여기에 있는 것이다. Type Erasure을 하면서 바이트 코드에서는 결국 raw types에서 변하는 것과 똑같은 형태로 바뀌게 되는데 이것이 모든 객체의 조상 Object로 취급되어 처리되는 것이다. 따라서 primitive 타입의 경우는 Object 타입을 상속 받지 않고 있기 때문에 타입 파라미터의 값으로 넣어주지 못하는 것이다.

```java
import java.util.ArrayList;
import java.util.List;

public class GenericExample<T> {
    public static void main(String[] args) {
        List<Integer> l = new ArrayList<Integer>();
        List<Integer> l2 = new ArrayList<>();
        List<String> l3 = new ArrayList<>();
        List rawList = new ArrayList();
    }
}
```



```java
 public static main([Ljava/lang/String;)V
   L0
    LINENUMBER 6 L0
    NEW java/util/ArrayList
    DUP
    INVOKESPECIAL java/util/ArrayList.<init> ()V
    ASTORE 1
   L1
    LINENUMBER 7 L1
    NEW java/util/ArrayList
    DUP
    INVOKESPECIAL java/util/ArrayList.<init> ()V
    ASTORE 2
   L2
    LINENUMBER 8 L2
    NEW java/util/ArrayList
    DUP
    INVOKESPECIAL java/util/ArrayList.<init> ()V
    ASTORE 3
   L3
    LINENUMBER 9 L3
    NEW java/util/ArrayList
    DUP
    INVOKESPECIAL java/util/ArrayList.<init> ()V
    ASTORE 4
   L4
    LINENUMBER 10 L4
    RETURN
   L5
    LOCALVARIABLE args [Ljava/lang/String; L0 L5 0
    LOCALVARIABLE l Ljava/util/List; L1 L5 1
    // signature Ljava/util/List<Ljava/lang/Integer;>;
    // declaration: l extends java.util.List<java.lang.Integer>
    LOCALVARIABLE l2 Ljava/util/List; L2 L5 2
    // signature Ljava/util/List<Ljava/lang/Integer;>;
    // declaration: l2 extends java.util.List<java.lang.Integer>
    LOCALVARIABLE l3 Ljava/util/List; L3 L5 3
    // signature Ljava/util/List<Ljava/lang/String;>;
    // declaration: l3 extends java.util.List<java.lang.String>
    LOCALVARIABLE rawList Ljava/util/List; L4 L5 4
    MAXSTACK = 2
    MAXLOCALS = 5
}
```

Decompile된 파일을 보면 다음과 같다.

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

import java.util.ArrayList;

public class GenericExample<T> {
    public GenericExample() {
    }

    public static void main(String[] args) {
        new ArrayList();
        new ArrayList();
        new ArrayList();
        new ArrayList();
    }
}

```

#### 참고자료

https://docs.oracle.com/javase/tutorial/java/generics/erasure.html