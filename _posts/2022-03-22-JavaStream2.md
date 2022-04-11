---
layout: single
title:  "자바 Stream 활용"
published: true
categories: JAVA
tag: [Java, java_lambda]
toc: true
---

# 스트림 활용

## 스트림 API가 지원하는 다양한 연산

### 필터링
#### predicate
* filter메서드 ->predicate(boolean 반환 함수)를 인수로 받아 프레티 케이트와 일치하는 모든 요소를 포함하는 스트림을 반환한다.

```java
List<Dish> dishes = menu.stream()
                .filter(Dish::isVegetarian) //채식요리인지 확인하는 메서드
                        .collect(toList());
```

#### 고유요소
* 고유 요소로 이루어진 스트림을 반환하는 distinct 메서드를 지원한다.

```java
List<Integer> numbers = Arrays.asList(1,2,1,3,3,2,4);
List<Integer> evenNumbers = numbers.stream()
        .filter(i -> i % 2 == 0)
        .distinct()        //한개의 요소만
        .collect(Collectors.toList());
```    
## 슬라이싱

### predicate 이용
#### takeWhile
 filter를 이용할 경우 조건에 대해 모든 요소르 확인하지만, takewhile은 조건이 참이 아닐경우 거기서 멈춘다 -> 정렬이 되어있는 경우 사용하기 좋다
* 무한 스트림을 포함한 모든 스트림에 프레디케이트를 적용해 스트림을 슬라이스 할 수 있다.
```java
Stream.of(2,4,3,4,5,6,7)
        .takeWhile(n -> n%2 == 0)
        .forEach(System.out::println);  //2,4
```

#### dropWhile
 takeWhile의 나머지 요소를 찾을 수 있다. 즉 프레디케이트가 거짓이 되면 그 지점에서 작업을 중단하고 남은 요소를 반환한다
 
 ```java
Stream.of(2,4,3,4,5,6,7,8,9)
        .dropWhile(n -> n%2 == 0)
        .forEach(System.out::println); //3,4,5,6,7
```

### 스트림 축소 
 주어진 값 이하의 크기를 갖는 새로운 스트림을 반환하는 limit(n) 메서드를 지원한다.

 ```java
Stream.of(1,2,3,4,5,6,7)
        .filter(i -> i > 2)
        .limit(2)                       //요소 2개를 반환한다
        .forEach(System.out::println);  //3,4
```

### 요소 건너뛰기
 처음 n개 요소를 제외한 스트림을 반환하는 skip(n) 메서드를 지원한다.

 ```java
Stream.of(1,2,3,4,5,6,7)
        .filter(i -> i > 2)
        .skip(2)                        //2보다 큰 2개의 요소를 건너뜀
        .forEach(System.out::println);  //5,6,7
```

### 매핑
### 스트림 각 요소에 함수 적용하게
스트림은 함수를 인수로 받는 map 메서드를 지원한다.

```java
List<String> words = Arrays.asList("abcd", "aa", "b", "abcde");
List<Integer> wordLengths = words.stream()
        .map(String::length)           //문자의 길이 추출
        .collect(toList());            //4,2,1,5
```
->map을 이용하여 각 단어의 길이를 리스트로 반환할 수 있다.

### 스트림 평면화
 문자의 길이 뿐 아니라 고유 문자로 이루어진 리스트를 반환 할 수 있다.

1. map과 Arrays.stream 활용
```java
String[] arrayOfWord = {"Hello", "World"};
Stream<String> streamOfWord = Arrays.stream(arrayOfWord); 
//문자열을 받아 스트림을 만드는 메서드
```

2.flatMap 사용
```java
List<String> hello = Arrays.asList("hello", "world");

List<String> words = hello.stream()
        .map(word -> word.split(""))    //각 단어를 대별 문자를 포함하는 배열로 변환
        .flatMap(Arrays::stream)        //생성된 스트림을 하나의 스트림으로 평면화
        .distinct()
        .collect(toList()); //h e l o w r d
```
map(Arrays::stream)은 Stream<<String[]>>인데 반해 flatMap은 하나의 평면화된 스트림을 반환하게 되어 Stream<<String>>의 형태가 된다.

### 검색과 매칭
#### 프레디케이트가 적어도 한 요소와 일치하는지 확인 : anyMatch

```java
//베지테리안 메뉴가 1개 이상 존재하면 문구 출력
if(menu.stream().anyMatch(Dish::isVegetarian)){
    System.out.println("The menu is vegetarian friendly!");
}
```

#### 모든 요소와 일치하는지 확인 : allMatch

```java
//모든 음식이 1000칼로리 이하인지 체크
boolean isHealthy = menu.stream()
            .allMatch(dish -> dish.getCalories() < 1000);
```

#### noneMatch
일치하는 요소가 없는지 확인(allMatch와 반대기능)
```java
//1000칼로리가 넘어가는 음식이 없는지 확인한다.
boolean isHealthy = menu.stream()
        .noneMatch(dish -> dish.getCalories() > 1000);
```
#### findAny
 현재 스트림에서 임의의 요소를 반환한다.
 ```java
Optional<Dish> dish =
        menu.stream()
                .filter(Dish::isVegetarian)
                .findAny(); //임의의 채식 매뉴 반환
 ```

 *Optiona<T>
  값이 존재나 부재 여부를 표현하는 컨테이너 클래스. null은 쉽게 에러를 발생시킬 수 있어 만들어졌다.

  ```java
  .ifPresent(dish -> System.out.println(dish.getName()));
  //값이 있으면 출력이 시행되고, 없으면 아무일도 일어나지 않는다.
  ```

  ### 첫번째 요소 찾기 : findFirst

  ```java
List<Integer> numbers = Arrays.asList(1,2,3,4,6);
Optional<Integer> first = 
        numbers.stream()
                .map(n -> n*n)
                .filter(n -> n % 3 == 0)
                .findFirst(); //9 조건에 맞는 첫번째 요소
  ```
  -반환 순서가 상관없다면 findAny를 사용한다

### 리듀싱
 스트림 요소를 조합하여 더 복잡한 질의를 수행할 수 있다. 이러한 질의를 수행하기 위해 Integer 같은 결과가 나올 때까지 스트림의 모든 요소를 반복적으로 처리해야하는데 이를 리듀싱 연산이라고 한다

#### 요소의 합


