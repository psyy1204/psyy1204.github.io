---
layout: single
title:  "자바 제네릭(Generic)"
published: true
categories: JAVA
tag: [Java]
toc: true
---

# 제네릭(Generic)

- 사전적 의미는 '포괄적인, 총칭(통칭)의' 또는 '(상품, 특히 약품이) 회사 이름이 붙지 않은, 일반 명칭으로 판매되는' 이라는 뜻이다. -> '일반적인'이라는 의미
- 클래스 내부에서 지정하는 것이 아닌 외부에서 사용자에 의해 지정되는 것을 의미

​		-> 타입을 미리 지정해주는 것이 아닌 필요에 의해 지정할 수 있도록 하는 일반(Generic) 타입



### 제네릭의 장점

1. 잘못된 타입이 들어올 수 있는 것을 컴파일 단계에서 방지한다.
2. 클래스 외부에서 타입을 지정하기 때문에 타입을 체크하도 반환할 필요가 없다.
3. 비슷한 기능을 지원하는 경우 코드 재사용성이 높아진다

|   타입    |      설명       |
| :-------: | :-------------: |
|   < T >   |      Type       |
|   < E >   |     Element     |
|   < K >   |       Key       |
|   < V >   |      Value      |
|   < N >   |     Number      |
| S,U,V ... | 2nd, 3rd, 4th.. |



### 클래스 및 인터페이스 선언

```java
public class ClassA <T> {  }
public Interface InterfaceA <T> {  }
//T타입은 {} 안에서 유효

public class HashMap <K, V> {  }
//인자는 두개일 수도 있다

ClassA<String> a = new ClassA<String>();
//사용은 이렇게 한다 T -> String이 된다
```

- 주의할 점은 참조타입만 가능하고 iint, double, char 같은 primitive type은 올 수 없다



### 제네릭 클래스

다음과 같이 사용할 수 있다.

```java
class ClassA<T> {
    private T t;

    public void setT(T t) {
        this.t = t;
    }
    public T getT() {
        return t;
    }
}

public class Generic {
    public static void main(String[] args) {
        ClassA<Integer> a = new ClassA<Integer>();
        a.setT(1);

        System.out.println("a : " + a.getT());
    }
}

//결과
a : 1
```



### 제네릭 메서드

- 선언방법

  :클래스와는 달리 반환타입 이전에 제네릭 타입을 선언한다

```java
	public   <T>     T    GenericMethod     (T t) { ... }
//[접근제어자]<제네릭>[반환타입] [메소드명]     [제네릭타입] [파라미터]
```

```java
class ClassA<T> {
    private T t;
    public void setT(T t) { this.t = t; }
    public T getT() {   return t;   }

    <T> T genericMethod(T t) {
        return t;
    }
}

public class Generic {
    public static void main(String[] args) {
        ClassA<String> a = new ClassA<String>();
        ClassA<Integer> b = new ClassA<Integer>();

        a.setT("a");
        b.setT(1);

        System.out.println("a Generic Type: " + a.getT().getClass().getName());
        System.out.println("b Generic Type: " + b.getT().getClass().getName());
        System.out.println("b GenericMethod 사용시: " +
                "" + b.genericMethod(2.0).getClass().getName());
    }
}

//실행결과
a Generic Type: java.lang.String
b Generic Type: java.lang.Integer
b GenericMethod 사용시: java.lang.Double
```

- genericMethod()는 파라미터 타입에 따라 T 타입이 결정된다.

​		->클래스에서 지정한 제네릭 유형과 별도로 메소드에서 독립적으로 제네릭 유형을 선언하여 사용이 가능

- 정적 메소드로 선언할때 필요 -> 객체 생성없이 클래스 이름을 통해 바로 쓸 수 있다



### 제네릭의 허용과 제한

| 제네릭          | 범위                                                         |
| --------------- | ------------------------------------------------------------ |
| < K extends T > | T와 T의 자손 타입만 가능 (K는 들어오는 타입으로 지정 됨)     |
| < K super T >   | T와 T의 부모(조상) 타입만 가능 (K는 들어오는 타입으로 지정 됨) |
| <? extends T>   | T와 T의 자손 타입만 가능                                     |
| <? super T>     | T와 T의 부모(조상) 타입만 가능                               |
| <?>             | 모든 타입 가능. <? extends Object>랑 같은 의미               |

