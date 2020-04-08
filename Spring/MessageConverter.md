# HttpMessageConverter

Spring에서 `HttpMessageConverter`는 Http Request, Response에서 데이터타입인 Json, xml 등을 Java Bean으로 변환하거나 그 반대인 Java Bean을 Json, Xml로 변환하는 역할을 한다. 

### 1. 언제, 어떤 용도로 사용할까?

Client와 Server 환경을 생각해보자. Client는 Server 구성 환경에 대해 알 필요가 있을까? Server를 구현한 프로그래밍 언어가 C, JAVA, 혹은 파이썬 등 특정 프로그래밍 언어에 맞게 Request 요청을 해야한다면 굉장히 Client와 Server간의 의존도가 높은 어플리케이션이라고 볼 수 있다. 그래서 현재 통용되고 있는 JSON 타입으로 Client와 Server간의 의사소통을 통해 Serverless한 환경을 구축하여 서로 의존관계 없이 확장 혹은 개발해 나아간다. 

그렇다면 이제 Server 입장에서 생각해보자. Client는 JSON 타입의 Request를 요청하였다면 이 Request를 이제는 Server 프로그래밍 환경에 맞춰 `변환` 해 줄 필요가 있다. 여기서는 Java Bean 규약에 맞게 변환이 필요하여 Spring이 지원하는 `HttpMessageConverter`를 사용하는 것이다. 다시 Client에게 Response를 할 때에도 Java Bean으로 되어있는 객체를 Json 타입으로 변환하여 줘야하기 때문에 Converter를 사용하여 Client에게 전달 할 수 있는 것이다. 

`HttpMessageConverter`는 Client와 Server간의 커뮤니케이션을 위한 변환기이다. 


### 1. Spring의 기본으로 제공되는 HttpMessageConverter
 
 **Default**
- ByteArrayHttpMessageConverter : Byte 배열에 맞춘 Converter, `MediaType`은 `모든 유형`을 지원한다. `Content-Type`은 `application/octet-stream`을 지원한다. 
- StringHttpMessageConverter : 문자열에 맞춘 Converter, `MediaType`은 `모든 유형`을 지원한다. `Content-Type`은 `text/plain`
- ResourceHttpMessageConverter : Spring의 Interface Resource 타입을 읽는데 사용되는 Converter이다.  
- SourceHttpMessageConverter : Source Object타입에 맞춘 Converter, `MediaType`은 `text/xml`, `application/xml`, `application/*-xml`
- FormHttpMessageConverter : HTML form 데이터 변환하는 Converter

**라이브러리 추가 시**  
해당 Converter들은  라이브러리를 추가하면 Spring 특정 버전 이상에서 해당 라이브러리를 찾아서 자동으로 Bean으로 등록해주는 것도 있고, 특정 버전 이하는 별도로 Bean을 등록해야한다.  
- MappingJacksonHttpMessageConverter : JSON 타입에 맞춘 Converter
- MappingJackson2HttpMessageConverter : JSON 타입에 맞춘 Converter
- Jaxb2RootElementHttpMessageConverter : Java Object를 Xml 혹은 반대로 Converter 


### 3. 동작과정 살펴보기

![HttpMessageConverter](/Spring/img/HttpConverter.png/)

위으 그림의 순서도를 살펴보면, Client로부터 들어온 Http Request에서 Content-Type을 분석한다음에 Server에 등록된 Converter를 찾아서 Java Bean Object로 변환해 준다. 만약, Content-Type에 해당되는 Converter가 등록되어 있지 않다면, 오류가 발생한다. 

여기서 Java Bean Object로 바인딩 과정 중에 @RequestBody나 @ModelAttribute를 사용하여 바인딩하게 된다. 반대로 Response를 보낼 때에는 
Java Bean Object를 Content-Type에 따른 Converter가 변환하여 Client에게 Resonse값을 내려주게 된다. 


### 정리

HttpMessageConverter는 Server와 Client간의 의사소통 과정중에 좀더 Server환경에 유연하게 사용할 수 있도록 지원하는 API라는 것을 느꼈다.

사실, 요새는 Spring 프로젝트 혹은 SpringBoot 프로젝트 버전이 높으면 이러한 Converter 설정을 지원을 잘해줘서 Custom을 하지 않는 이상 설정하는 일이 많지 않아 이러한 개념을 잊고 있었다.

새로 투입된 프로젝트가 엄청 오래된 레거시 프로젝트에 Spring 버전이 낮아 PrintWriter를 사용하여 IO 방식으로 JSON 값을 내려주는 것을 보고 Converter 설정과정중에 한번 개념 정리가 필요하다고 생각하여 정리하게 됬는데 꽤나 유익하였다. 

[Refference]
- [jcombat](http://www.jcombat.com/spring/understanding-http-message-converters-in-spring-framework)
- [javadevjournal](https://www.javadevjournal.com/spring/spring-http-message-converter/)