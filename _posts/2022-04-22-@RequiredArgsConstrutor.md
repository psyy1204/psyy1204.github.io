---
layout: single
title:  "[Spring] @RequiredArgsConstructor"
published: true
categories: Spring
tag: [Spring, Spring framework, lombok]
toc: true
---



# @RequiredArgsConstructor

## @RequiredArgsConstructor 이란?

* final 필드에 대해 생성자를 만들어주는 lombok annotation
* Spring Framework 의 DI 중 Constructor Injection(생성자 주입)을 임의의 코드 없이 자동으로 설정

** 의존성을 주입할 객체에 final 키워드를 사용하는 이유는 한번 의존성을 주입하게 되면 프로그램이 끝날때 까지 변하지 않는 특징을 가지므로 불변성을 표시해 주는게 좋기 때문이다.

## 사용 예제

-사용전

```java
@Controller
@RequestMapping("/members")
public class MemberController {

    private final MemberService memberService;
  
  	@Autowired
  	public MemberController(MemberService memberService) {
      this.memberService = memberService;
    }
}

```

-사용 후

```java
@Controller
@RequestMapping("/members")
@RequiredArgsConstructor
public class MemberController {

    private final MemberService memberService;
 
}
```



## 사용시 주의사항

* final 키워드를 사용하지 않고 사용하면 NullPointerException이 발생한다.