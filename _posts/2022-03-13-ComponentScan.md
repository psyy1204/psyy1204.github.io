---
layout: single
title:  "[Spring] Component Scan"
published: true
categories: Spring
tag: [Java, Spring, Spring framework]
toc: true
---

# 컴포넌트 스캔

## 컴포넌트 스캔이란? 

- 스프링이 직접 클래스를 검색해서 빈으로 등록해주는 기능
- 설정 클래스에 빈으로 등록하지 않아도 원하는 클래스를 빈으로 등록 할 수 있어 설정코드를 줄일 수 있다

## @Component 애노테이션

- 스프링이 검색해서 빈으로 등록하려면 @Component 애노테이션을 붙여야한다. 

MemberDao class

```java
@Component
public class MemberDao {

	private static long nextId = 0;

	private Map<String, Member> map = new HashMap<>();

	public Member selectByEmail(String email) {
		return map.get(email);
	}
 ...생략
```

ChangePassword 클래스와 MemberRegister 클래스에도 @Component 애노테이션을 붙여주었다

```java
@Component
public class ChangePasswordService
```

```java
@Component
public class MemberRegisterService
```

@Component 애노테이션에 값을 주었는지에 따라 빈으로 등록할 때 사용할 이름이 결정된다. 위의 클래스들은  "memberDao"와 같이 첫글자를 소문자로 바꾼 이름이 사용되고, 아래의 경우 "infoPrinter"가 빈의 이름으로 사용된다.

```java
@Component("infoPrinter")
public class MemberInfoPrinter
```

```java
@Component("listPrinter")
public class MemberListPrinter
```



## @ComponentScan 애노테이션

@Component  애노테이션을 붙인 클래스를 스캔해서 스프링 빈으로 등록하려면 @ComponentScan 애노테이션을 적용해야한다

설정클래스에 적용하면된다 ->예제는 AppCtx

```java
@Configuration
@ComponentScan(basePackages = {"spring"})
//basePackages : 스캔대상 페이지 목록을 지정해 준다
public class AppCtx {

	@Bean
	@Qualifier("printer")
	public MemberPrinter memberPrinter1(){
		return new MemberPrinter();
	}

	@Bean
	@Qualifier("summaryPrinter")
	public MemberSummaryPrinter memberPrinter2() {
		return new MemberSummaryPrinter();
	}

	@Bean
	public VersionPrinter versionPrinter() {
		VersionPrinter versionPrinter = new VersionPrinter();
		versionPrinter.setMajorVersion(5);
		versionPrinter.setMinorVersion(0);
		return versionPrinter;
	}
}
```

스프링 컨테이너가 @Component 애노테이션을 붙인 클래스를 검색한여 빈으로 등록해 주기때문데 설정 클래스가 간단해진다.

```java
//processNewCommand 메서드
MemberRegisterService regSvc = 
			ctx.getBean( MemberRegisterService.class);

//processChangeCommand 메서드
ChangePasswordService changePwdSvc =
			ctx.getBean(ChangePasswordService.class);
```

MemberRegisterService 타입 빈 객체 이름은 "memberRegisterService"이고, ChangePasswordService의 이름은 "changePasswordService" 이다.

위와같이 타입만으로 구하도록 사용할 수 있다.

## 스캔 대상에서 제외, 포함 하기

### excludeFilters

:스캔할 때 특정 대상을 자동 등록 대상에서 제외할 수 있다.

```java
@Configuration
@ComponentScan(basePackages = { "spring" },
		excludeFilters = @Filter(type = FilterType.REGEX, pattern = "spring\\..*Dao"))
public class AppCtx {
    
	@Bean
	public MemberDao memberDao() {
		return new MemberDao();
	}
```

* FilterType.REGEX : 정규표현식을 사용해서 제외 대상을 지정한다는 것을 의미

* pattern속성 : FilterType에 적용할 값 -> 예제는 "spring"으로 시작하고 Dao로 끝나는 정규 표현식을 지정하고 있다. 

```java
@Configuration
@ComponentScan(basePackages = { "spring" },
		excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = {Noproduct.class, ManualBean.class})
public class AppCtx 
```

* FilterType.ANNOTATION : 필터로 사용할 애노테이션 타입을 값으로 준다. ->예제는 @MannualBean 애노테이션을 제외대상 클래스로 추가했다

* FilterType.ASPECTJ :  AspectJ 패턴을 사용하여 대상을 지정한다.

```java
@Configuration
@ComponentScan(basePackages = { "spring" },
		excludeFilters = @Filter(type = FilterType.ASSIGNABLE_TYPE, classes = MemberDao.class))
public class AppCtx
```

* FilterType.ASSIGNABLE_TYPE :  특정 타입이나 그 하위 타입을 컴포넌트 스캔 대상에서 제외하고 싶을때 사용한다.                   classes 속성에 제외할 타입 목록을 지정한다.

## ComponentScan 스캔 대상

* 다음 애노테이션이 붙은 클래스가 스캔 대상에 포함된다
  * @Component(org.springframewoork.stereotype pkg)
  * @Controller(org.springframewoork.stereotype pkg)
  * @Service(org.springframewoork.stereotype pkg)
  * @Repository(org.springframewoork.stereotype pkg)
  * @Aspect(org.aspectj.lang.annoation pkg)
  * @Configuration(org.springframework.context.annotation pkg)



## ComponentScan 충돌 처리

### 빈 이름 충돌

* 서로 다른 타입인데 같은 빈 이름을 사용하는 경우가 있다면 둘 중 하나에 명시적으로 빈 이름을 지정하여 충돌을 피해야 한다.
* 스캔시 사용하는 빈 이름과 수동 등록한 빈 이름이 같을 경우 수동 등록한 빈이 우선한다.



[참고]

-초보 웹 개발자를 위한 스프링5 프로그래밍 입문, 최범균 저
