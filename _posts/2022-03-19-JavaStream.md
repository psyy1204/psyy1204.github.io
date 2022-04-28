---
layout: single
title:  "[JAVA] 자바 Stream"
published: true
categories: JAVA
tag: [Java, java_lambda]
toc: true
---

# 스트림

## 스트림이란 무엇인가?

* 스트림은 자바8 API에 새로 추가된 기능이다. 스트림을 이용하면 선언형(즉, 데이터를 처리하는 임시 구현 코드 대신 질의로 표현할 수 있다.)으로 컬렉션 데이터를 처리할 수 있다.
* 멀티스레드 코드를 구현하지 않아도 데이터를 투명하게 병렬로 처리할 수 있따.

특징

* **선언형** : 더 간결하고 가독성이 좋아진다.
* **조립할 수 있음** :유연성이 좋아진다.
* **병렬화** :  성능이 좋아진다.

## 스트림 시작하기

스트림이란 '데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소(Sequence of elements)'로 정의할 수 있다.

#### 스트림의 정의

* **연속된 요소**: 스트림은 특정 요소 형식으로 이루어진 연속된 값 집합의 인터페이스를 제공한다. Filter, sorted, map처럼 표현 계산식이 주를 이룬다
* **소스**: 스트림은 컬렉션, 배열, I/O자원 등의 데이터 제공 소스로부터 데이터를 소비한다. 정렬된 컬렉션으로 스트림을 생성하면 정렬이 그대로 유지된다.
* **데이터 처리 연산**: 스트림은 함수형 프로그래밍 언어에서 일반적으로 지원하는 연산과 데이터베이스와 비슷한 연산을 지원한다.



#### 스트림의 중요 특징

* **파이프라이닝(Pipelining)** : 대부분의 스트림 연산은 스트림 연산끼리 연결해서 커다란 파이프 라인을 구성할 수 있도록 스트림 자산을 반환한다. ->게으름(laziness), 쇼트서킷(short-circuiting)같은 최적화도 얻을 수 있다.
* **내부 반복** : 반복자를 이용해 명시적으로 반복하는 컬렉션과 달리 스트림은 내부 반복을 지원한다.

예제

```java
List<String> threeHighCaloricDishNames =
        menu.stream()       
  //메뉴(요리 리스트)에서 스트림을 얻는다 (데이터 소스 : 연속된 요소를 스트림에 저공)
             .filter(dish -> dish.getCalories() > 300)  		// 첫번쨰로 고칼로리 요리를 필터링
             .map(Dish::getName)                         // 요리명 추출
             .limit(3)                                   // 선착순 3개
             .collect(toList());                         // 결과를 다른 리스트로 저장
System.out.println(threeHighCaloricDishNames);

//실행결과
[pork, beef, chicken]
```

* filter : 람다를 인수로 받아 스트림에서 특정 요소를 제외시킨다.  dish -> dish.getCalories() > 300 *필터링*
* map: 람다를 이용해서 한 요소를 다른 요소로 변환하거나 정보를 추출한다. Dish::getName(d -> d.getName( )) *추출* 
* limit: 정해진 개수 이상의 요소가 스트림에 저장되지 못하게 스트림 크기를 축소 truncate한다. *축소*
* collect: 스트림을 다른 형식을 변환한다. 예제는 스트림을 리스트로 반환했다. 

## 스트림과 컬렉션

데이터를 언제 계산하는지가 컬렉션과 스트림의 가장 큰 차이이다.

* **컬렉션** : 현재 자료구조가 포함하는 모든 값을 메모리에 저장하는 자료구조다. 즉 컬렉션의 모든 요소는 컬렉션에 추가하기 전에 계산되어야 한다. 적극적으로 생성된다(생산자 중심: 팔기도 전에 창고를 가득 채움) -> 전체 자료구조가 저장되어있는 DVD도 컬렉션이다.
* **스트림** : 이론적으로 요청할 때만 요소를 계산하는 고정된 자료구조이다. 사용자가 데이터를 요청할 때만 값을 계산한다.(요청 중심 제조 또는 즉석 제조) ->인터넷 스트리밍, 미리 내려받은 프레임부터 재생할 수 있다. 또는 구글 검색시 모든 검색결과를 내려받을 때까지 기다리지 않아도 10개 또는 20개 결과요소를 포함하는 스트림을 얻을 수 있다.

### 딱 한번만 탐색할 수 있다.

탐색된 스트림의 요소는 소비된다. 한 번 탐색한 요소를 다시 탐색하려면 새로운 스트림을 만들어야 한다.

### 외부 반복과 내부 반복

* 외부반복 : 컬렉션 인터페이스를 사용하려면 사용자가 직접 요소를 반복해야 한다.(for-each 등 사용)
* 내부반복 : 스트림 라이브러리는 반복을 알아서 처리하고 결과 스트림값을 어딘가에 저장해준다.

내부적으로 숨겨졌던 반복자를 사용한 외부 반복 ->명시적으로 컬렉션 항목을 하나씩 가져와서 처리한다.

```java
List<String> names = new ArrayList<>();
Iterator<Dish> iterator = menu.iterator();
while (iterator.hasNext()) {			//명시적 반복
    Dish dish = iterator.next();
    names.add(dish.getName());
}
```

스트림: 내부 반복

```java
List<String> names = menu.stream()
        .map(Dish::getName)     //map메서드를 getName메서드로 파라미터화해서 요리명을 추출한다.
        .collect(toList());     //파이프라인을 실행한다. 반복자는 필요없다
```

## 스트림 연산



### 중간연산

filter나 sorted같은 중간 연산은 다른 스트림을 반환한다. 여러 중간 연산을 연결해서 질의를 만들 수 있다. 중요한 특징은 단말 연산을 스트림 파이프 라인에 실행하지 전까지는 아무 연산도 수행하지 않는다는 것이다. 중간 연간을 합친 다음에 합쳐진 중간 연산을 최종 연산으로 한번에 처리하기 때문이다.

```java
List<String> names =
            menu.stream()
                    .filter(dish -> {
                        System.out.println("filtering: " + dish.getName());
                        return dish.getCalories() > 300;
                    }) 	//필터링한 요리명 출력
                    .map(dish -> {
                        System.out.println("mapping : " + dish.getName());
                        return dish.getName();
                    })	//추출한 요리명 출력
                    .limit(3)
                    .collect(toList());
    System.out.println(names);
}
//실행결과
filtering: pork
mapping : pork
filtering: beef
mapping : beef
filtering: chicken
mapping : chicken
[pork, beef, chicken]
```

->filter와 map은 서로 다른 연산이지만 한 과정으로 병합되었다.

### 최종연산

스트림 파이프라인에서 결과를 도출한다. 보통 최종 연산에 의해 List, Integer, void 등 스트림 이외의 결과가 반환된다,

```java
menu.stream().forEach(System.out::println);
```

### 스트림 이용하기

1. 질의를 수행할(컬렉션 같은) 데이터 소스
2. 스트림 파이프라인을 구성할 중간 연산 연결
3. 스트림 파이프라인을 실행하고 결과를 만들 최종 연산

스트림 파이프라인의 개념은 빌더 패턴과 비슷하다.

중간연산 표

| 연산     | 형식      | 반환형식    | 연산의 인수     | 함수 디스크립터 |
| -------- | --------- | ----------- | --------------- | --------------- |
| filter   | 중간 연산 | Stream< T > | Predicate< T >  | T -> boolean    |
| map      | 중간 연산 | Stream< R > | Function<T , R> | T -> R          |
| limit    | 중간 연산 | Stream< T > |                 |                 |
| sorted   | 중간 연산 | Stream< T > | Comparator< T > | (T , T) -> int  |
| distinct | 중간 연산 | Stream< T > |                 |                 |

최종연산 표

| 연산    | 형식      | 반환 형식     | 목적                                                         |
| ------- | --------- | ------------- | ------------------------------------------------------------ |
| forEach | 최종 연산 | void          | 스트림의 각 요소를 소비하면서 람다를 적용한다.               |
| count   | 최종 연산 | long(generic) | 스트림의 요소 개수를 반환한다.                               |
| collect | 최종 연산 |               | 스트림을 리듀스 해서 리스트, 맵, 정수 형식의 컬렉션을 만든다. |

