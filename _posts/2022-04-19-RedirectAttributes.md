---
layout: single
title:  "[Spring] RedirectAttributes"
published: true
categories: Spring
tag: [Spring, Spring framework, Java_project]
toc: true
---

# RedirectAttributes

 프로젝트를 만드는 중에 저장완료와 같은 알람을 띄우고싶었다.
그걸위해 발견한 RedirectAttributes에서 제공하는 addFlashAttribute!
그래서 RedirectAttributes를 좀 더 정리해보기로 했다.

 인터페이스의 구성은 다음과같다
```java
public interface RedirectAttributes extends Model {

	@Override
	RedirectAttributes addAttribute(String attributeName, @Nullable Object attributeValue);

	@Override
	RedirectAttributes addAttribute(Object attributeValue);

	@Override
	RedirectAttributes addAllAttributes(Collection<?> attributeValues);

	@Override
	RedirectAttributes mergeAttributes(Map<String, ?> attributes);

	/**
	 * Add the given flash attribute.
	 * @param attributeName the attribute name; never {@code null}
	 * @param attributeValue the attribute value; may be {@code null}
	 */
	RedirectAttributes addFlashAttribute(String attributeName, @Nullable Object attributeValue);

	/**
	 * Add the given flash storage using a
	 * {@link org.springframework.core.Conventions#getVariableName generated name}.
	 * @param attributeValue the flash attribute value; never {@code null}
	 */
	RedirectAttributes addFlashAttribute(Object attributeValue);

	/**
	 * Return the attributes candidate for flash storage or an empty Map.
	 */
	Map<String, ?> getFlashAttributes();
}
```
## RedirectAttributes란
 리다이렉트의 특징은 발생하면 요청이 끊어지고, 새로운 http GET 요청이 시작된다. 때문에 이전 데이처는 소멸한다. 따라서 리다이렉트로 모델을 전달하는 것은 의미가 없다 따라서 스프링은 데이터를 전달 하는것이 가능하도록 이 메서드를 제공하는것이다

  RedirectAttributes는 핸들러에서 요청을 처리한 뒤 데이터를 함께 전달한다. 리다이렉트가 발생하기 전 모든 속성을 세션에 복사하고 리다이렉션 이후 저장된 플래시 속성을 세션에서 모델로 이동시킨다. 헤더에 파라미터를 붙이지 않아 URL에 노출되지 않는다. 데이터를 전달하는 다른 방법으로는 session과 requestparam 등이 있다

 ### FlashAttribute 이용하여 저장완료 처리해보기
  FlashAttribute는 일회성으로 새로고침하면 데이터가 소멸한다!