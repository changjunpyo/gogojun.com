---
title: '[Effective-Java] Item58. 전통적인 for문 보다 for-each문을 사용하라'
date: 2021-04-18 23:04:77
category: effective-java
thumbnail: { thumbnailSrc }
draft: false
---

기본적으로 우리는 반복문을 사용하는 경우 for문을 사용하게 된다. 물론 while문을 사용하는 경우도 있지만 for문보다 더 길고 복잡한 코드가 되기 쉽다. 

### 전통적 for문

```java
for (초기화; 조건문; 증감문){
  실행구문
}
```



배열을 순회하거나 `Collection` 을 순회하는 방법을 전통적 for문을 사용하기 위해서 아래와 같이 사용한다.

```java
// Array
for (int i=0; i< a.length; i++){
  // a[i]를 통한 접근 및 변경
}
// Collection
for (Iterator<Element> i = c.iterator; i.hasNext;){
  Element e = i.next();
	// e를 사용
}
```



### 전통적 for문의 문제점

1. 변수 사용(i, e, 등등)이 많아 오타 or 잘못 사용 가능성이 높아진다.
2. 컬렉션 / 배열에 따라서 사용 방법이 달라 주의해야 한다.



### for-each문(advanced for statement)

```java
for (Element e: elements){
  // e를 사용
}
```

`:` 을 "in"이라고 읽으면 된다. 따라서 이 반복문은 "elements안의 각 원소 e에 대해"라고 읽는다.

for문을 사용하든 for-each문을 사용하든 속도는 동일하다. for-each문이 만들어내는 코드는 사람이 최적화한 코드와 동일하기 때문이다.

하지만 `Collection`을 중첩해서 순회하는 경우 for-each문의 이점이 더 드러난다. 

```java
enum Die {ONE, TWO, THREE, FOUR, FIVE, SIX}
Collection<DIE> dice = EnumSet.allof(Die.class);

// 2개의 주사위를 던질 때 나오는 모든 경우의 수 출력 (잘못된 코드)
for (Iterator<Die> i = dice.iterator(); i.hasNext();){
  for (Iterator<Die> j = dice.itrator(); j.hasNext();){
    System.out.println(i.next() + " " + j.next());
  }
}

// 2개의 주사위를 던질 때 나오는 모든 경우의 수 출력 (수정된 코드)
for (Iterator<Die> i = dice.iterator(); i.hasNext();){
  Die d = i.next();
  for (Iterator<Die> j = dice.itrator(); j.hasNext();){
    System.out.println(d + " " + j.next());
  }
}
```

 전통적 for문을 이용하여 코드를 순회하면 다음과 중첩된 코드를 작성하게 되면 오류가 있는 코드를 작성하기 쉽고, 수정하더라도 가독성이 떨어지는 코드이기 쉽다. 아래는 for-each문을 이용한 코드이다.

```java
for (Die d1: dice){
  for (Die d2: dice){
    System.out.println(d1 + " " + d2);
  }
}
```

훨씬 깔끔하게 코드를 작성 가능하다.

### for-each문을 쓰지 못하는 경우

- 컬렉션을 순회하면서 선택된 원소를 제거할  필요가 있는 경우
- 리스트나 배열 등의 컬렉션을 순회하면서 값 일부와 전체를 수정이 필요한 경우
- 여러 컬렉션을 병렬적으로 순회해야하는 경우 인덱스 변수를 명시해 정확하게 돌아가도록 작성해야한다.



### for-each문이 가능한 경우

- 배열, 컬렉션은 기본이고 `Iterable` 인터페이스를 구현한 모든 객체라면 for-each문을 사용할 수있다.

```java
public interface Iterable<E> {
  Iterable<E> iterator();
}
```

`Iterable`을 처음부터 구현하는 것이 까다롭기는 하지만, 원소들의 묶음을 표현하는 타입을 작성해야한다면 `Iterable` 을 구현해도록 해보자.

#### 참조
*1. Effective java 3/e , by Joshua Bloch*