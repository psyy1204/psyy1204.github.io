---
layout: single
title:  "스프링 AOP"
published: true
categories: JAVA
tag: [Java, Spring, Spring framework]
toc: true
---

# AOP 프로그래밍

## 프로젝트 준비

- 스프링 프레임워크의 AOP기능은 spring - aop모듈이 제공하는데 spring-context 모듈을 의존대상에 추가하면 함께 포함된다

- aspectjweaver 모듈은 AOP설정에 필요한 에노테이션을 제공하므로 이 의존을 추가해햐 한다.

  ```java
  //build.gradle
  compile 'org.aspectj:aspectjweaver:1.8.13'
  //pom.xml
  <dependency>
  	<groupId>org.aspectj</groupId>
  	<artifactId>aspectjweaver</artifactId>
  	<version>1.8.13</version>
  </dependency>
  ```

## 프록시와 AOP

Caculator 인터페이스

```java
public interface Calculator {   public long factorial(long num);   }
```

for문을 이용한 factorial

```java
public class ImpeCalculator implements Calculator{
    @Override
    public long factorial(long num) {
        long result = 1;
        for(long i = 1; i <= num; i++) {
            result *= i;
        }
        return result;
    }
}
```

재귀함수를 이용한 factorial

```java
public class RecCalculator implements Calculator {
    @Override
    public long factorial(long num) {
        if (num == 0) return 1;
        else {
            return num * factorial(num - 1);
        }
    }
}
```

- 시작시간과 끝시간을 기록하기 위해서 factorial 메서드의 시작과 끝에 시간을 구해서 차이를 출력하는 코드를 추가거나 메서드 실행 전후에 값을 구하는 방법이 있다.
  - 시간의 단위를 수정해야하는 경우 일일히 코드를 수정해야 하는 번거로움이 있고, 코드의 중복을 피할 수 없다.

------------->이를위해 **프록시객체**를 사용한다.

```java
public class ExeTimeCalculator implements Calculator {

	private Calculator delegate;
	//생성자를 통해 Calculator 객체를 전달받아 delegate필드에 할당
	public ExeTimeCalculator(Calculator delegate) {
        this.delegate = delegate;
    }

	@Override
	public long factorial(long num) {
		long start = System.nanoTime();
		long result = delegate.factorial(num); // delegate.factorial()의 실행시간을 구한다
		long end = System.nanoTime();
		System.out.printf("%s.factorial(%d) 실행 시간 = %d\n",
				delegate.getClass().getSimpleName(),
				num, (end - start));
		return result;
	}
}
```

실행하면 다음과 같은 결과가 나온다.

```java
public class MainProxy {
    public static void main(String[] args) {
        ExeTimeCalculator calculator = new ExeTimeCalculator(new ImpeCalculator());
        System.out.println(calculator.factorial(20));

        ExeTimeCalculator calculator2 = new ExeTimeCalculator(new RecCalculator());
        System.out.println(calculator2.factorial(20));
    }
}
//실행결과
ImpeCalculator.factorial(20) 실행 시간 = 24276700
2432902008176640000
RecCalculator.factorial(20) 실행 시간 = 7400
2432902008176640000
```

->기존코드를 변경하지 않아도 된다, 실행시간의 단위를 변경하고 싶다면 ExeTimeCalculator클래스만 변경한다.

**ExeTimeCalculator 클래스(프록시)**  
  ---->ImpeCalculator객체 ,RecCalculator객체 (프록시의 대상) : 팩토리얼 연산 자체(핵심 기능)를 한다

- factorial() 기능자체를 구현하기 보다 다른 객체에 factorial() 실행을 위임했다
- 계산기능 외에 다른 부가적인 기능(실행 시간 측정)을 실행한다

**핵심 기능은 다른 객체에 위임하고 부가적인 기능을 제공하는 객체를 프록시(Proxy)라고 부른다**



##  AOP

#### AOP(Aspect Oriented Programming) - 관점 지향 프로그래밍

: 여러 객체에 공통으로 적용할 수 있는 기능을 분리해서 재사용성을 높여주는 프로그래밍 기법

->핵심 기능과 공통 기능의 구현을 분리함으로써 핵심 기능을 구현한 코드의 수정없이 공통 기능을 적용할 수 있음

:핵심 기능의 코드를 수정하지 않으면서 공통 기능의 구현을 추가하는 것이다. 

- 컴파일 시점에 코드에 공통 기능을 삽입하는 방법
- 클래스 로딩 시점에 바이트 코드에 공통 기능을 삽입하는 방법
- 런타임에 프록시 객체를 생성해서 공통 기능을 삽입하는 방법 (스프링이 제공하는 AOP)

:스프링 AOP는 프록시 객체를 자동으로 만들어준다 ->공통 기능을 구현한 클래스만 알맞게 구현하면 된다.



**AOP 주요 용어**

| 용어       | 의미                                                         |
| ---------- | ------------------------------------------------------------ |
| Advice     | 언제 공통 관심 기능을 핵심로직에 적용할 지 정의. <br />예를들어 '메서드를 호출하기 전(언제)'에 '트랜잭션 시작(공통기능)' 기능을 적용한다는 것 |
| Jointpoint | Advice 적용 가능한 지점. 메서드 호출, 필드값 변경 등이 해당<br />스프링은 프록시를 이용해 AOP를 구현하기 때문에 메서드 호출에 대한 Jointpoint만 지원한다 |
| Pointcut   | Jointpoint의 부분 집합으로 실제 Advice가 적용되는 Jointpoint<br />스프링에서는 정규 표현식이나 AspectJ 문법을 이용하여 Pointcut을 정의할 수 있다 |
| Weaving    | Advice를 핵심 로직 코드에 적용하는 것                        |
| Aspect     | 여러 객체에 공통으로 적용되는 기능(공통기능). 트랜잭션이나 보안 등이 좋은 예 |

### Advice의 종류

**구현 가능한 Advice 종류**

| 종류                   | 설명                                                         |
| ---------------------- | ------------------------------------------------------------ |
| Before Advice          | 대상 객체 메서드 호출 전에 공통 기능을 실행                  |
| After Returning Advice | 대상 객체의 메서드가 익셉션 없이 실행된 이후 공통 기능 실행  |
| After Throwing Advice  | 대상 객체의 메서드를 실행하는 도중 익셉션이 발생한 경우에 공통 기능 실행 |
| After Advice           | 익셉션 발생 여부 상관없이 대상 객체 메서드 실행 후 공통 기능 실행<br />(try-catch-finally의 finally블록과 유사) |
| **Around Advice**      | 대상 객체의 메서드 실행 전, 후 또는 익셉션 발생 시점에 공통 기능 실행 |

## 스프링 AOP 구현

절차

- Aspect로 사용할 클래스에 @Aspect 어노테이션을 붙인다
- @Pointcut 애노테이션으로 공통 기능을 적용할 Pointcut을 정의한다
- 공통 기능을 구현한 메서드에 @Around 애노테이션을 붙인다



### @Aspect, @Pointcut, @Around 이용한 AOP 구현

공통기능

```java
//메서드 실행 전후(Around Advice) 사용할 공통 기능
@Aspect
public class ExeTimeAspect {

    @Pointcut("execution(public * chap07..*(..))")
    //실제 advice가 적용되는 jointPoint
    //공통 기능 적용할 대상 설정
    //chap07 패키지와 그 하위 패키지에 위치한 타입의 public 메서드를 PointCut으로 설정
    private void publicTarget() {   }

    @Around("publicTarget()")
    //publicTarget() 메서드에 정의한 Pointcut 공통 기능을 적용하겠다는 것
    public Object measure(ProceedingJoinPoint joinPoint) throws Throwable {
        //ProceedingJoinPoint: 프록시 대상 객체의 메서드 호출 시
        long start = System.nanoTime();
        try {
            Object result = joinPoint.proceed(); //proceed() 실제 대상 객체의 메서드를 호출한다
                                                 //이 코드 전후에 공통기능 코드 위치시킨다
            return result;
        } finally {
            long finish = System.nanoTime();
            Signature sig = joinPoint.getSignature();
            System.out.printf("%s.%s(%s) 실행 시간 : %d ns\n",
                    joinPoint.getTarget().getClass().getSimpleName(),
                    sig.getName(), Arrays.toString(joinPoint.getArgs()),
                    (finish-start));
        }
    }
}
```

설정 클래스

```java
@Configuration
@EnableAspectJAutoProxy
//@Aspect 붙은 클래스를 공통기능으로 적용하기 위함
// -> 스프링이 @Aspect가 붙은 빈 객체를 찾아서 빈 객체의 @Pointcut과 @Around를 사용한다.
public class AppCtx {
    @Bean
    public ExeTimeAspect exeTimeAspect() {
        return new ExeTimeAspect();
    }
    @Bean
    public Calculator calculator() {
        return new RecCalculator();
    }
}
```

AppCtx를 사용하여 스프링 컨테이너 생성

```java
public class MainAspect {

    public static void main(String[] args) {

        AnnotationConfigApplicationContext ctx
                = new AnnotationConfigApplicationContext(AppCtx.class);

        Calculator cal = ctx.getBean("calculator", Calculator.class);
        long fiveFact = cal.factorial(5);
        System.out.println("cal.factorial(5) = " + fiveFact);
        System.out.println(cal.getClass().getName());
        ctx.close();
    }
}
```

실행결과

```java
RecCalculator.factorial([5]) 실행 시간 : 41600 ns
    //ExeTimeAspect 클래스의 measure() 메서드 출력
cal.factorial(5) = 120
com.sun.proxy.$Proxy17 //스프링이 생성한 프록시 타입
```



### ProceedingJointPoint 메서드

ProceedingJointPoint 인터페이스가 제공하는 메서드

- Signature getSignature() : 호출되는 메서드에 대한 정보를 구한다
- Object getTarget() : 대상 객체를 구한다
- Object[] getArgs() : 파라미터 목록을 구한다



org.aspectj.lang.Signature 인터페이스가 제공하는 메서드

- String getName() : 호출되는 메서드의 이름을 구한다

- String toLongString() : 호출되는 메서드를 완전하게 표현한 문장을 구한다(메서드의 리턴타입, 파라미터 타입 모두 표시)

- String toShortString() : 호출되는 메서드를 축약해서 표현한 문장을 구한다(기본 구현은 메서드 이름만 구함)

  *자바에서 메서드 이름+파라미터를 메서드 시그니처라고 한다



## 프록시 생성 방식

 스프링은 AOP를 위한 프록시 객체를 생성할 때 실제 생성할 빈 객체가 인터페이스를 상속하면 인터페이스를 이용해서 프록시를 생성한다.

```java
@Bean
//AOP적용시 RecCalculator가 상속받은 Calculator 인터페이스를 이용해서 프록시 생성
public Calculator calculator() {	return new RecCalculator();		}

//실제 타입이 Calculator를 상속한 프록시 타입이므로
//RecCalculator로 변환을 할 수 없어 아래의 코드는 익셉션이 발생한다 
RecCalculator cal = ctx.getBean("calculator", RecCalculator.class)
```
![proxy](/images/2022-03-16-springDI/proxy.png)
-> 빈의 실제 타입이 RecCalculator라고 하더라도 "calculator" 이름에 해당하는 빈 객체의 타입은 그림처럼 Calculator 인터페이스를 상속받은 프록시 타입이 된다.

```java
@Configuration
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class AppCtx {
```

->인터페이스가 아닌 자바 클래스를 상속받아 프록시를 생성하게 하려면 위와같이 EnableAspectJAutoProxy를 설정하면 된다

### execution 명시자 표현식

excution 명시자는  Advice를 적용할 메서드를 지정할 때 사용한다.

``` java
excution(수식어패턴? 리턴타입패턴 클래스이름패턴?메서드이름패턴(파라미터 패턴))
```

