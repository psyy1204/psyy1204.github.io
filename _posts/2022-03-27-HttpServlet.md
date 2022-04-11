---
layout: single
title:  "HttpServlet"
published: true
categories: Spring
tag: [Java, Spring, Spring framework]
toc: true
---

# HttpServlet

## HttpServletRequest

* 서블릿은 HTTP 요청 메세지를 편리하게 사용할 수 있도록 HTTP 요청 메세지를 파싱하고 그 결과를  HttpServletRequest객체에 담아 제공한다.
* WAS는 브라우저로부터 요청을 받으면 요청시 가지고 있는 정보를 HttpServeletRequest 객체를 생성하여 저장하고, 응답을 보낼때 사용하기 위해 HttpServletResponse 객체를 생성하여 생성된 HttpServeletRequest,HttpServletResponse 객체를 서블릿에게 전달한다.
* http프로토콜의 request정보를 서블릿에게 전달하기 위한 목적으로 사용
* 헤더정보, 파라미터, 쿠키, URI, URL 등의 정보를 읽어 들이는 메소드를 가지고 있음.
* Body의 Stream을 읽어 들이는 메소드를 가지고 있음.



* HTTP 요청 메시지

```http
POST /save HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded

username=park$age=20
```

* Start line : HTTP메소드, URL, 쿼리 스트링, 스키마, 프로토콜
* 헤더: 헤더 조회
* 바디: form 파라미터 형식 조회, message body 데이터 직접 조회

### 임시 저장소 기능

-해당 HTTP 요청이 시작부터 끝날때까지 유지되는 임시 저장소

* 저장 : request.setAttribute(name, value)
* 조회: request.getAttribute(name)

### 세션 관리 기능

* request.getSession(create:true)



## HttpServletResponse

- WAS는 어떤 클라이언트가 요청을 보냈는지 알고 있고, 해당 클라이언트에게 응답을 보내기 위한 HttpServleResponse객체를 생성하여 서블릿에게 전달.
- 서블릿은 해당 객체를 이용하여 content type, 응답코드, 응답 메시지등을 전송.