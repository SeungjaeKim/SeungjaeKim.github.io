---
layout: post
title:  "스프링 Interceptor 사용"
date:   2019-11-20
excerpt: "스프링 Interceptor 사용 설정 기본은 이렇게 쓰자"
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

<figure>
	<img src="/assets/img/img_01.png">
	<figcaption>스프링 MVC 요청의 라이프 사이클</figcaption>
</figure>

## 인터셉터 사용하기
* URI를 호출 했을때 해당 요청의 컨트롤러가 처리 되기 전 또는 후에 작업을 하기 위해 사용
* 필터(Filter) 와 인터셉터(Interceptor)로 수행 가능

## 필터와 인터셉터의 차이
* 필터는 디스패처 서블릿(DispatcherServlet) 앞에서 동작 / 인터셉터는 디스패처 서블릿에서 핸들러 컨트롤러로 가기 전에 동작 합니다.
* 필터는 J2EE 표준 스펙에 있는 서블릿의 기능 중 일부 / 스프링 프레임워크에서 제공 되는 기능
* 인터셉터는 필터와 달리 스프링 빈을 사용 가능
* 문자열 인코딩과 같은 웹 전반에서 사용되는 기능은 필터로 구현
* 클라이언트의 요청과 관련이 있는 여러 가지 처리(ex 로그인, 인증, 권한 등)는 인터셉터로 구현

## HandlerInterceptorAdapter로 인터셉터 구현하기
* preHandle - 컨트롤러 실행 전에 수행
* postHandle - 컨트롤러 수행 후 결과를 뷰로 보내기 전에 수행
* afterCompletion - 뷰의 작업까지 완료된 후 수행

## Log의 시작과 끝에 인터셉터 사용하기
* interceptor 패키지 생성
* LoggerInterceptor 클래스 생성 후 내용 입력

{% highlight java %}
@Slf4j
public class LoggerInterceptor extends HandlerInterceptorAdapter{

	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		log.debug("================================= START =================================");
		log.debug(" Request URI \t: " + request.getRequestURI());
		return super.preHandle(request, response, handler);
	}

	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			ModelAndView modelAndView) throws Exception {
		log.debug("================================= END ====================================\n");
		super.postHandle(request, response, handler, modelAndView);
	}
}
{% endhighlight %}

## Interceptor 등록하기
* Configuration 패키지 생성
* WebMvcConfiguration 클래스 생성 후 내용 입력
* addPathPatterns() 메서드 - 요청 주소의 패턴 지정
* excludePathPatterns() 메서드 - 제외할 요청 주소의 패턴을 지정

```Java
public class WebMvcConfiguration implements WebMvcConfigurer {

	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		registry.addInterceptor(new LoggerInterceptor())
				//.addPathPatterns("/**")
				//.excludePathPatterns("/admin/**")
				;
	}
}
```