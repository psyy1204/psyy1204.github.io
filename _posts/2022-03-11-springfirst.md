---
layout: single
title:  "JAVA_Spring 입문"
published: true
categories: JAVA, Spring
tag: [Java, Spring, Spring framework]
toc: true
---

# 스프링

## 스프링이란?

### 스프링의 역사

:과거 자바 진영의 표준 기술인 EJB(Enterprise Java Beans)의 문제점을 지적하면서, 2002년 로드 존슨에 의해 등장했다

:2002년 로드 존슨이 출간한 책에는 EJB 없이 확장가능한 애플리케이션을 개발 할 수 있음을 보여주고, 30,000라인 이상의 예제 코드를 선보였다. 이곳의 지금 스프링 핵심 개념과 기반 코드가 들어있다

:BeanFactory, ApplicationContext, POJO, 제어의 역전, 의존관계 주입

:책 출간 직후 Juergen Hoeller(유겐 휠러), Yann Caroff(얀 카로프)가 로드 존슨에게 오픈소스 프로젝트를 제안

:스프링의 이름은 전통적인 J2EE(EJB)라는 겨울을 넘어 새로운 시작이라는 뜻!

<img src="/images/2022-03-11-springfirst/road.png" 
width="300px" height="230px" title="rod_Johnson" alt="rod_Johnson">

<img src="/images/2022-03-11-springfirst/hibernate.png" 
width="300px" height="230px" title="hibernate" alt="hibernate">

### 스프링의 개념

-JAVA로 다양한 어플리케이션을 만들기 위한 프로그래밍 툴이라고 할 수 있다.

-한가지로 정의하기는 어렵다. 여러가지 기술들의 모음이다.

-가장 잘 알려진 정의는 "자바 엔터프라이즈 개발을 편하게 해주는 오픈소스 경량급 애플리케이션 프레임워크 이다.

-흔히 스프링이라고 하면 스프링 프레임 워크를 말한다.

![spring](/images/2022-03-11-springfirst/spring.png)

-스프링은 좋은 객체 지향 애플리케이션을 개발 할 수 있게 도와주는 프레임워크 이다.



### 스프링 프레임 워크의 주요 특징

-의존 주입(Dependency Injection: DI) 지원

-AOP(Aspect-Oriented Programming) 지원

-MVC 웹 프레임워크 제공

-JDBC, JPA 연동, 선언적 트랜잭션 처리 등 DB 지원 연동

-외에도 스케쥴링, 메세지 연동(JMS), 이메일 발송, 테스트 지원 등 자바 기반의 어플리케이션을 개발하는데 필요한 다양한 기능을 제공한다.



장점

-Tomcat 같은 웹 서버를 내장해서 별도의 웹 서버를 설치할 필요가 없다.

-손쉬운 빌드 구성을 위한 starter종속성 제공

-스프링과 3rd parth(외부) 라이브러리 자동구성

​	-> version 충돌을 막을 수 있다.(version까지 지정을 해서 다운로드 해준다)

-관례에 의한 간결한 설정







