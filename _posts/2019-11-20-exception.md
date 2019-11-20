---
layout: post
title:  "스프링 Exception 처리"
date:   2019-11-20
excerpt: "스프링 Exception 처리 방법"
categories: Spring
tag:
- spring
- springboot
- log
- logger
- slf4
- log4jdbc
comments: true
---

## 예외처리(Exception) 하기 
1. try/catch 를 이용한 예외처리
2. 각각의 컨트롤러단에서 @ExceptionHandler를 이용한 예외처리
3. @ControllerAdvice를 이용한 전역 예외처리

## ControllerAdvice 사용하기
* 스프링 3.2에 추가된 어노테이션
* common 패치키 밑에 ExceptionHandler 클래스를 생성 후 코드 작성

{% highlight java %}
@Slf4j
@ControllerAdvice
public class ExceptionHandler {
	
    @org.springframework.web.bind.annotation.ExceptionHandler(Exception.class)
	public ModelAndView defaultExceptionHandler(HttpServletRequest request, Exception exception) {
		ModelAndView mv = new ModelAndView("/error/error_default");
		mv.addObject("exception", exception);
		
		log.error("exception", exception);
		return mv;
	}
}
{% endhighlight %}

## 예외 처리 화면 추가
* template 폴더 밑에 error 폴더 생성 error_default.html 생성 후 코드 작성

{% highlight html %}
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>공통 에러 페이지</title>
<link rel="stylesheet" th:href="@{/css/style.css}" /> 
</head>
<body>
	<p>공통 에러 페이지</p>
	<p th:text="${exception}"></p>
	<ul th:each="list : ${exception.getStackTrace()}"
		th:text="${list.toString()}"></ul>
</body>
</html>
{% endhighlight %}