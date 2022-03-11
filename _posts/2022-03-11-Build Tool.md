---
layout: single
title:  "Build tool - Gradle과 Maven"
published: true
categories: Spring
tag: [Java, Spring, Build tool, Gradle, Maven]
toc: true
---

# JAVA Build Tool

## 빌드관리도구

-프로젝트에서 필요한 xml, properties, jar 파일들을 자동으로 인식하여 빌드해주는 도구를 말한다.

-소스코드 컴파일 테스트, 정적분석 등을 하여 실행 가능한 앱으로 빌드를 해준다.

-프로젝트 정보관리, 테스트 빌드, 배포 등의 작업을 진행해 준다.

-외부 라이브러리를 참조하여 자동으로 다운로드 및 업데이트 해준다

자바의 대표 빌드 도구: Ant, Maven, Gradle

![buildtool](/images/2022-03-11-Build Tool/buildtool.png)


## Ant

-자바 대표 관리 도구였던, Ant를 대체하기 위해 개발되었다

-xml 기반의 빌드 스크립트이다

-자유로운 빌드 단위 지정이 가능하다

-간단, 사용이 쉽다

-대규모 프로젝트에서는 복잡해진다

-라이프 사이클이 없다



## Maven

-xml 기반의 빌드 스크립트이다

-라이프 사이클을 도입했다

-pom.xml로 편리하게 Dependency를 관리 할 수 있다



* pom.xml의 역할(Project Object Model)

  * 프로젝트 정보의 관리
  * 해당 프로젝트에서 사용되는 외부 라이브러리 관리
  * 해당 프로젝트의 빌드 관리 설정

* maven 설정파일

  * project : 최상위 엘리먼트
  * modelVersion : POM모델의 버젼
  * groupId : 프로젝트 생성하는 조직의 고유 아이디
  * artifactId : 프로젝트 식별하는 유일한 아이디
  * packaging : 어떤 형태로 패키징 할건지 결정 : jar, wae, ear, pom....
  * dependencies: 프로젝트와 의존관계에 있는 라이브러리

* 라이프 사이클

![lifeCycle](/images/2022-03-11-Build Tool/lifecycle.png)

[출처] https://www.slideshare.net/ssuser5445b7/ss-56566336?qid=927855f5-7c8a-4f88-a834-d31292324fd2&v=&b=&from_search=4

## Gradle

-빌드 스크립트를 간단하게 작성할 수 있다

-스크립트가 비교적 간단하여 이해하기가 쉽다

-Ant task, Ant build script, maven repository를 이용할 수 있어 과거 자원을 활용할 수 있다(단, 메이븐 프러그인은 사용불가)

-Groovy 언어를 기반으로 만들어졌다

-멀티프로젝트의 빌드를 지원한다

-Gradle의 설치 없이 Gradle Wrapper를 이용하여 빌드를 지원한다

-build.gradle 파일에 빌드 정보를 정의하여 환경설정, 라이브러리, 빌드 방식 등을 기술하여 프로젝트의 관리 환경을 구성한다

![gradle](/images/2022-03-11-Build Tool/gradle.png)

* Gradle의 설정파일
  * plugins :  프로젝트 빌드 위해 컴파일, jar 파일 생성과 같은 작업을 해주는 plugin 지정
  * repositories: 저장소 정보를 관리하는 프로퍼티
  * dependencies : 의존성에 관한 설정을 관리하는 프로퍼티. 필요한 라이브러리를 기술하면 그 라이브러리를 참조할 수 있다
    * implementation : 컴파일 시 접근
    * testImplementation : 테스트 컴파일 시 접근
    * compileOniy : 컴파일시만 사용
    * runtimeOnly : 런타임시만 사용
    * annotationProcessor : lombok 사용시 필수로 추가되어야 하는 지시어, 미포함하면 compiler가 lombok에서 제공하는 어노테이션을 인식할수 없음
    * exclude : 해당 의존성 중 제외할 의존성 정의
  * ext : gradle의 모든 task에서 사용 가능, 일종의 전역변수
  * buildscript : 빌드하는 동안 필요한 처리를 모아 둔 곳