---
layout: single
title:  "Spring Bean"
published: true
categories: Spring
tag: [Java, Spring, SpringBean]
toc: true
---



## 스프링은 객체 컨테이너다.

* 스프링은 객체를 생성하고 초기화하는 기능을 제공한다, 스프링이 생성하는 객체를 bean(빈) 이라고 부른다
* 작성할 예제 클래스
  * Greeter.java : 콘솔에 간단한 메세지를 출력하는 자바 크래스
  * AppContext.java : 스프링 설정파일
  * Main.java: main() 메서드를 통해 스프링과 Greeter를 실행하는 자바 클래스

```java
package chap02;

public class Greeter {

    private String format;

    public String greet(String guest) {
        return String.format(format,guest);
        //메세지 생성
        //String.format  --> 문자열의 형식을 설정하는 메서드
    }

    public void setFormat(String format) {
        this.format = format;
        //문자열 포맷을 지정
    }
}
```

```java
package chap02;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
//설정 클래스로 지정
public class AppContext {

    @Bean
    //스프링이 생성하는 객체 ->빈, 해당메서드가 생성한 객체를 스프링이 관리함
    public Greeter greeter() {
        Greeter g = new Greeter();
        g.setFormat("%s, 안녕하세요!"); //Greeter객체 초기화
        return g;
    }
}
```

**@Configuration** - 스프링을 사용하려면 먼저 스프링이 어떤 객체를 생성하고, 의존을 어떻게 주입할지 정의한 **설정정보**가 필요하다. 이 어노테이션을 붙이면 스프링 설정 클래스로 이용할 수 있다

**@bean** - 해당 메서드가 생성한 객체를 스프링이 관리하는 빈 객체로 등록한다 메서드의 이름은 빈 객체를 구분할 때 사용된다

- 스프링 컨테이너의 빈 객체 관리-내부적으로 빈 객체와 빈 이름을 연결하는 정보를 갖는다 Greeter 타입의 객체를 greeter라는 이름의 빈으로 설정하면 컨테이너는 연결한 정보를 관리한다.

<img src="/images/2022-03-11-SpringBean/beanFactory.png" 
width="300px" height="230px" title="beanFactory" alt="beanFactory">

![BeanFactory2](/images/2022-03-11-SpringBean/beanFactory2.png)

- **BeanFactory** -가장 상위에 존재하는 인터페이스, 객체 생성과 검색에 대한 기능을 정의한다. <u>get bean()</u>메서드가 정의되어 있다 객체 검색 외에도 싱글톤/프로토 타입 빈인지 확인하는 기능도 제공한다.

- **AnnotationConfigApplicationContext** 클래스 - 자바 설정에서 정보를 읽어와 빈 객체를 생성하고 관리한다

- **get bean()** - AnnotationConfigApplicationContext가 자바 설정을 읽어와 빈(bean)객체를 검색할때 사용된다. getbean(빈객체이름(@bean 메서드이름), 검색할 빈 객체의 타입)

- GenericXmlApplicationContext- Xml로부터 객체 설정 정보를 가져온다

  

``` java
package chap02;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {

    public static void main(String[] args) {
        AnnotationConfigApplicationContext ctx =
                //자바설정에서 정보를 읽어와서 빈 객체를 생성하고 관리
                new AnnotationConfigApplicationContext(AppContext.class);
                //AppContext에 정의한 @Bean 설정정보를 읽어와서 Greeter 객체를 생성하고 초기화함

        Greeter g = ctx.getBean("greeter", Greeter.class);
                            //빈 이름, 빈 타입
                            //ctx가 자바 설정을 읽어와 생성한 bean 객체를 검색할때 사용
        String msg = g.greet("스프링");
        System.out.println("msg = " + msg);

        Greeter c = ctx.getBean("greeter", Greeter.class);
                                //빈 이름, 빈 타입
                            //ctx가 자바 설정을 읽어와 생성한 bean 객체를 검색할때 사용
    }
}
```

다음을 실행하면 생성한 Greeter 객체의 format은 "%s, 안녕하세요!")이다. 따라서 다음처럼 출력된다.

```java
3월 11, 2022 5:55:11 오후 org.springframework.context.support.AbstractApplicationContext prepareRefresh
정보: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@5f4da5c3: startup date [Fri Mar 11 17:55:11 KST 2022]; root of context hierarchy
msg = 스프링, 안녕하세요!
```






## 싱글톤(Singleton) 객체

스프링은 한 개의 객체만을 생성한다->'싱글톤 범위를 갖는다' 라고 표현한다

@Bean 하나당 하나의 객체를 생성한다.

- > 스프링은 설정 클래스를 그대로 사용하지 않는다. 대신 설정 클래스를 상속한 새로운 설정 클래스를 만들어 사용한다. 한번 생성한 객체를 보관했다가 이후에 동일한 객체를 리턴한다

```java
Greeter g = ctx.getBean("greeter", Greeter.class);       
String msg = g.greet("스프링");
System.out.println("msg = " + msg);

Greeter c = ctx.getBean("greeter", Greeter.class);
String msg1 = c.greet("스프링");
System.out.println("\nmsg1 = " + msg1);
```

다음을 실행하면 결과에서 g,c가 같은 객체임을 확인 할 수 있다

```java
org.springframework.context.support.AbstractApplicationContext prepareRefresh
정보: Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@5f4da5c3
msg = 스프링, 안녕하세요!
   
msg1 = 스프링, 안녕하세요!
3월 11, 2022 5:55:11 오후 org.springframework.context.support.AbstractApplicationContext doClose
정보: Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@5f4da5c3
```



[참고]

-초보 웹 개발자를 위한 스프링5 프로그래밍 입문, 최범균 저