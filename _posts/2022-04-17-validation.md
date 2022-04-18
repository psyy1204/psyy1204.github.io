---
layout: single
title:  "Validation"
published: true
categories: Spring
tag: [Java, Spring, Spring framework]
toc: true
---

# Validation(검증)

## map을 이용한 validation 처리 1

 폼 입력간 오류가 발생했을때 오류 화면으로 바로 이동하게 되면 사용자는 작성하던 내용을 다 잃어버리게 된다.
따라서 컨트롤러의 중요한 역할 중 하나는 HTTP 요청이 정상인지 검증 할 수 있어야 한다.

클라이언트 검증, 서버 검증
-클라이언트 검증은 보안이 취약하다.
-서버만으로 검증하면 고객 사용성이 부족해진다
-둘을 잘 섞어 사용하고, 최종적으로 서버검증은 필수다
-API스펙을 잘 정의해서 검증 오류를 API 응답 결과에 잘 남겨야 한다

실패시
컨트롤러에서 검증 로직실행 -> 모델에 기존정보, 오류내용 포함하여 상품등록 폼을 재 랜더링

```Java
@PostMapping("/add")
public String addItem(@ModelAttribute Item item, RedirectAttributes redirectAttributes,
                          Model model) {

    //검증 오류 결과를 보관
    Map<String,String> errors = new HashMap<>();

    //검증 로직
    if(!StringUtils.hasText(item.getItemName())) {
        //글자가 없다면
        errors.put("itemName","상품 이름은 필수 입니다.");
    }

    //복합 룰 검증
    if(item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if(resultPrice < 10000) {
            errors.put("globalError", "가격 * 수량의 합은 10,000원 이상이여야 합니다. 현재값 = " + resultPrice);
        }
    }

    //검증에 실패하면 다시 입력 폼으로
    if(!errors.isEmpty()) {
        model.addAttribute("errors",errors);
        return "validation/v1/addForm";
    }
```
다음과 같이 errors라는 Map을 생성하여 모델에 전달하고, 뷰에서 이 데이터를 사용하여 고객에게 오류 메세지를 출력 할 수 있다.
@ModelAttribute Item item를 통해 item객체 정보가 들어간다. 따라서 전달된 값이 오류로 인해 입력폼으로 돌아오더라도 유지될 수 있다.


```javaScript
<!-- globalError 라는 오류 키가 있는지 확인-->
<div th:if="${errors?.containsKey('globalError')}">
    <p class="filed-error" th:text="${errors['globalError']}">전체 오류 메시지</p>
</div>
```
* 위와 같이 html문서를 작성하면 화면에서 오류 메세지를 출력할 수 있다.
* errors. 는 errors 가 null이면 NullPointerException 이 생긴다.
* errors?. 은 errors 가 null 일때 NullPointerException 이 발생하는 대신, null 을 반환하는
문법이다.
* th:if는 null을 실패로 처리해 준다

```javaScript
<input type="text" th:classappend="${errors?.containsKey('itemName')} ? 'fielderror'
: _" class="form-control">
```
* 다음과 같이 간단하게 표현이 가능하다. 값이 없으면 _ (No-Operation)을 사용해서 아무것도 하지 않는다.

## 스프링이 제공하는 검증방법 이용

### BindingResult
* BindingResult : 스프링이 제공하는 검증 오류를 보관하는 객체이다. 검증 오류가 발생하면 여기에 보관하면 된다.

* 컨트롤러를 다음과 같이 변경한다.
```java
@PostMapping("/add")
public String addItemV1(@ModelAttribute Item item, BindingResult bindingResult,
                        //오류가 생길경우 bindingResult에 담긴다, 자동으로 뷰에 같이 넘어간다
                        // BindingResult bindingResult 파라미터의 위치는 @ModelAttribute Item item 다음에 와야 한다.
                      RedirectAttributes redirectAttributes) {
    if(!StringUtils.hasText(item.getItemName())) {
        bindingResult.addError(new FieldError("item", "itemName", "상품 이름은 필수 입니다.")); //필드 단위 에러
    }

        if(item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if(resultPrice < 10000) {
            bindingResult.addError(new ObjectError("item", "가격 * 수량의 합은 10,000원 이상이여야 합니다. 현재값 = " + resultPrice));
                                    //특정 필드가 없음
        }
    }
```

* 필드에 오류가 있으면 FieldError 객체를 생성해서 bindingResult 에 담아둔다
* 특정 필드를 넘어서는 오류가 있으면 ObjectError 객체를 생성해서 bindingResult 에 담는다
-objectName : @ModelAttribute 이름
-field : 오류가 발생한 필드 이름
-defaultMessage : 오류 기본 메시지

```javaScript
        <div>
            <label for="itemName" th:text="#{label.item.itemName}">상품명</label>
            <input type="text" id="itemName" th:field="*{itemName}"
                   th:errorclass="field-error"
                   class="form-control" placeholder="이름을 입력하세요">
            <div class="field-error" th:errors="*{itemName}">
                <!--에러가있으면 출력 없으면 안함-->
                상품명 오류
            </div>
        </div>
```
 다음과 같이 간단해 진다!

 ### 타임리프 스프링 검증 오류 통합기능

- #fields : #fields 로 BindingResult 가 제공하는 검증 오류에 접근할 수 있다.
- th:errors : 해당 필드에 오류가 있는 경우에 태그를 출력한다. th:if 의 편의 버전이다.
- th:errorclass : th:field 에서 지정한 필드에 오류가 있으면 class 정보를 추가한다.