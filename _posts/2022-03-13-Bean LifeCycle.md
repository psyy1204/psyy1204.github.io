---
layout: single
title:  "Bean LifeCycle"
published: true
categories: Spring
tag: [Java, Spring, Spring framework]
toc: true
---

# 컨테이너 초기화와 종료

## 컨테이너 초기화와 종료

* **스프링 컨테이너**는 초기화와 종료라는 라이프 사이클을 갖는다.

```java
//1.컨테이너 초기화
AnnotationConfigApplicationContext ctx =
   new AnnotationConfigApplicationContext(AppContext.class);
//2.컨테이너에서 빈 객체를 구해서 사용
Greeter g = ctx.getBean("greeter", Greeter.class);
String msg = g.greet("스프링");
//3.컨테이너 종료
ctx.close();
```

1.  스프링 컨테이너는 초기화 시 설정 클래스에서 정보를 읽어와 알맞은 빈 객체를 생성, 각 빈을 연결(의존 주입) 하는 작업을 수행
   - **컨테이너 초기화 -> 빈 객체 생성, 의존주입, 초기화**
2. 초기화 완료되면 getBean()과 같은 메서드를 이용해서 컨테이너에 보관된 빈 객체를 구한다
3. close()는 종료 메서드로 AbstractApplicationContext 클래스에 정의되어있다.
   - **컨테이너 종료 -> 빈 객체의 소멸**



## 스프링 빈 객체의 라이프 사이클

스프링 컨테이너는 빈 객체의 라이프 사이클을 관리한다.

- 빈 객체 라이프 사이클
  - 객체생성 -> 의존 설정 -> 초기화 -> 소멸

### 빈 객체의 초기화와 소멸 : 스프링 인터페이스

- 스프링 컨테이너는 빈 객체를 초기화 하고 소멸하기 위해 지정된 메서드를 호출한다.
  - org.sprinframework.beans.factory.InitializingBean
  - org.sprinframework.beans.factory.DisposableBean

**NetworkClient class**

```java
public class NetworkClient implements InitializingBean, DisposableBean {
			                           //초기화          //종료
    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출, url : " + url);
        connect();
        call("초기화 연결 메세지");
    }

    public void setUrl(String url) {
        this.url = url; 
    }
    
    //서비스 시작시 호출
    public void connect() {												
        System.out.println("connect: " + url);
    }
    //연결 한 상태-> 메세지를 던질 수 있다
    public void call(String message) {									
        System.out.println("call : " + url +" message : " + message);
    }
    //서비스 종료시 호출
    public void disconnect() {
        System.out.println("close: " + url);
    }
    @Override
    //InitializingBean 은 afterPropertiesSet() 메서드로 초기화를 지원한다.
    public void afterPropertiesSet() throws Exception {				
        System.out.println("NetworkClient.afterPropertiesSet");
        connect();
        call("초기화 연결 메세지");										
    }
    @Override
    //DisposableBean 은 destroy() 메서드로 소멸을 지원한다.
    public void destroy() throws Exception {					
        System.out.println("NetworkClient.destroy");
          disconnect();
    }
}
```

**Test class**

```java
public class BeanLifeCycleTest {

    @Test
    public void lifeCycleTest() {
        ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
        //close 쓰기위해서
        NetworkClient client = ac.getBean(NetworkClient.class);
        ac.close();
    }

    @Configuration
    static class LifeCycleConfig {
        @Bean
        public NetworkClient networkClient() {
            NetworkClient networkClient = new NetworkClient(); //객체생성을 먼저하고
            networkClient.setUrl("http://hello-spring.dev");   //값이 들어감(Url 세팅)
            return networkClient;
        }
    }
}
```

**실행결과**

```java
생성자 호출, url = null
NetworkClient.afterPropertiesSet						//초기화 메서드
connect: http://hello-spring.dev
call : http://hello-spring.dev message : 초기화 연결 메세지
23:37:48.746 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@101952da, started on Sun Mar 13 23:37:48 KST 2022
NetworkClient.destroy									//소멸 메서드 호출
close : http://hello-spring.dev						
```



### 초기화, 소멸 메서드 단점

- 이 인터페이스는 스프링 전용 인터페이스다. 해당 코드가 스프링 전용 인터페이스에 의존한다.

​			->코드를 스프링 의존적으로 작성해햐 한다.

- 초기화, 소멸 메서드의 이름을 변경할 수 없다. 
- 내가 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없다.

*지금은 거의 사용하지 않는 방법이다.



## 빈 객체의 초기화와 소멸 : 커스텀 메서드

직접 구현한 클래스가 아닌 외부에서 제공받은 클래스를 스프링 빈 객체로 설정하고 싶을때도 있다. 이 경우 직접 메서드를 지정할 수 있다. @Bean 태그에서 initMethod 속성과 destroyMethod 속성을 사용할 수 있다.



### 특징

- 메서드 이름을 자유롭게 줄 수 있다. 
- 스프링 빈이 스프링 코드에 의존하지 않는다. 
- <u>코드가 아니라 설정 정보를 사용하기 때문에 코드를 고칠 수 없는 외부 라이브러리에도 초기화, 종료 메서드를 적용할 수 있다</u>

스프링 빈의 이벤트 라이프사이클 

스프링 컨테이너 생성 ->스프링 빈 생성 ->의존관계 주입 ->초기화 콜백 ->사용 ->소멸전 콜백-> 스프링 종료(싱글톤 예)



초기화 콜백: 빈이 생성되고, 빈의 의존관계 주입이 완료된 후 호출 

소멸전 콜백: 빈이 소멸되기 직전에 호출



