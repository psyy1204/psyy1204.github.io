---
layout: single
title:  "동작 파라미터화 코드 전달하기"
published: true
categories: JAVA
tag: [Java, java_lambda]
toc: true
---

# 동작 파라미터화 코드 전달하기

-  동작 파라미터화(behavior parameterization)를 이용하면 자주 바뀌는 요구사항에 대해 효과적으로 대응할 수 있다.
- 동작 파라미터화란 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미한다.



## 변화하는 요구사항에 대응하기 

하나의 예제로 점차 개선하면서 유연해 지는 사례를 보여준다

### 1. 녹색 사과 필터링

- 사과의 색을 정의하는 Color enum

```java
public enum Color {    RED, GREEN	}
```

- 녹색 사과 선택을 위한 조건을 명시한 클래스

```java
    //1.녹색 사과를 찾을때 -> 빨간 사과를 찾으려면 똑같은 코드 1개를 추가해야 한다
    public static List<Apple> filterGreenApples(List<Apple> inventory) {
        List<Apple> result = new ArrayList<>();     // 사과 누적 리스트
        for(Apple apple : inventory) {
            if(GREEN.equals(apple.getColor())){     //녹색 사과만 선택한다
                result.add(apple);
            }
        }
        return result;
    }
```

​	->**비슷한 코드가 반복 존재하면 그 코드를 추상화하자!**

### 2. 색을 파라미터화

```java
    public static List<Apple> filterApplesByColor(List<Apple> inventory, Color color) {
        															//색을 파라미터화
        List<Apple> result = new ArrayList<>();
        for(Apple apple : inventory) {
            if(apple.getColor().equals(color)) {
                result.add(apple);
            }
        }
        return result;
    }
```

- 다음처럼 호출 할 수 있다

```java
List<Apple> greenApples = filterApplesByColor(inventory, Green);
```

- 단, 무게에 대응하기 위한 무게 정보 파라미터를 가진 클래스도 추가하고 나면 코드의 중복을 피할 수 없다
- 소프트웨어 공학의 DRY(Don't Repeat Yourself) 원칙을 어긴다!

### 3. 동작 파라미터화

- 선택을 결정하는 인터페이스를 정의한다
- 프레디케이트(Predicate) -> 참, 거짓을 반환하는 함수

```java
public interface ApplePredicate {    boolean test(Apple apple);		}
```

- 다음과 같이 여러 버전의 ApplePredicate를 정의할 수 있다

```java
    public class AppleHeavyWeightPredicate implements ApplePredicate {  //무거운 사과만 선택
        public boolean test(Apple apple) {
            return apple.getWeight() > 150;
        }
    }
    public class AppleGreenColorPredicate implements ApplePredicate {   //녹색 사과만 선택
        public boolean test(Apple apple) {
            return GREEN.equals(apple.getColor());
        }
    }
```

-> 이를 전략 디자인 패턴(strategy design pattern) 이라고 부른다

*전략 디자인 패턴 : 각 알고리즘(전략이라 불리는)을 캡슐화하는 알고리즘 패밀리를 정의해 둔 다음, 런타임에 알고리즘을 선택하는 기법 (ApplePredicate: 알고리즘 패밀리, AppleHeavyWeightPredicate:전략)



### 4. 추상적 조건으로 필터링

```java
    public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
        List<Apple> result = new ArrayList<>();
        for(Apple apple : inventory) {
            if(p.test(apple)) {    //프레디케이트 객체로 사과 검사 조건을 캡슐화
                result.add(apple);
            }
        }
        return result;
    }
```

이제 변화에 대응 할 수 있는 코드를 만들 수 있다.

```java
    static class AppleRedAndHeavyPredicate implements ApplePredicate {
        public boolean test(Apple apple) {
            return RED.equals(apple.getColor()) && apple.getWeight() > 150;
        }
    }
	//실행 위한 코드
    List<Apple> redAndHeavyApples =
            filterApples(inventory, new AppleRedAndHeavyPredicate());
```

- 전달된 ApplePredicate 객체에 의해 filterApples 메서드의 동작이 결정된다 
  - 즉, **filterApples 메서드의 동작을 파라미터화 한 것**

![parameter](/images/2022-03-14-JavaFirst/parameter.png)

- 컬렉션 탐색 로직과 각 항목에 적용할 동작을 분리할 수 있다는 것이 강점이다.



### 5. 복잡한 과정 간소화 : 익명클래스

- 자바는 클래스의 선언과 인스턴스화를 동시에 수행 할 수 있도록 익명 클래스 기법을 제공한다.
- 자바의 지역클래스(블록 내에 선언된 클래스)와 비슷한 개념이다

```java
        List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
                                            //filterApples메서드의 동작을 직접 파라미터화함
            public boolean test(Apple apple) {
                return RED.equals(apple.getColor());
            }
        });
```



### 6. **람다 표현식** 사용

- 자바8의 람다 표현식을 이용하면 다음과 같이 간단하게 구현이 가능하다

```java
List<Apple> result = filterApples(inventory, (Apple apple) -> 	
                                  RED.equals(apple.getColor()));
```



### 7. 리스트 형식으로 추상화

```java
	public interface Predicate<T> {		boolean test(T t);		}

    public  static <T> List<T> filter(List<T> list, Predicate<T> p) {
        List<T> result = new ArrayList<>();
        for (T e : list) {
            if(p.test(e)) {
                result.add(e);
            }
        }
        return result;
```

- 이제 사과뿐만 아니라 바나나, 오렌지, 정수 등에 사용할 수 있다

```java
List<Apple> redApples2 =
           filter(inventory, (Apple apple) -> RED.equals(apple.getColor()));
```



[참조] 모던 자바 인 액션 - 한빛미디어
