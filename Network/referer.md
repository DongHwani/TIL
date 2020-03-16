# HTTP Header의 Referer

### 1. Referer란?

 웹 브라우저로 월드 와이드 웹을 서핑할 때, 하이퍼링크를 통해서 각각의 사이트로 방문시 남는 흔적을 말한다.

예를 들어 A라는 웹 페이지에 B 사이트로 이동하는 하이퍼링크가 존재한다고 하자. 이때 웹 사이트 이용자가 이 하이퍼링크를 클릭하게 되면 웹 브라우저에서 B 사이트로 참조 주소(리퍼러)를 전송하게 된다. B 사이트의 관리자는 이 전송된 리퍼러를 보고 방문객이 A 사이트를 통해 자신의 사이트에 방문한 사실을 알 수 있다.

웹 사이트의 서버 관리자가 사이트 방문객이 어떤 경로로 자신의 사이트에 방문했는지 알아볼 때 유용하게 사용된다.

하지만, 리퍼러는 조작또한 가능하기 때문에 리퍼러 정보를 사용할 때에는 보안에 항상 주의해야 한다.

한편, HTTP 리퍼러를 정의한 RFC에서 'referrer'를 'referer'라고 잘못 친 것에서 기인하여 HTTP 리퍼러는 'HTTP referer'라고 불린다.

[출처:위키백과]]("https://ko.wikipedia.org/wiki/HTTP_%EB%A6%AC%ED%8D%BC%EB%9F%AC")


### 2. 리퍼러 확인

![Referer](/Network/img/리퍼러.png)

네이버 메인에서 스포츠에서 해외야구 클릭 후 Chrome에서 Network를 확인해보면, 아래에서 request Header에 referer가 담긴것을 확인 할 수 있다. 



![Referer](/Network/img/리퍼러확인.png)


### 3. 리퍼러 검증

Header의 referer를 확인함으로써 유효한 경로로 왔는지 체크 할 수 있다. CSRF공격에 대한 방어책으로도 사용하곤 한다.


~~~JAVA 

    public ResponseEntity referer(HttpServletRequest httpRequest){
       
        String referer = httpRequest.getHeader("referer");
       
        if(!referer.equals("https://www.naver.com/")){
            //유효하지 않은 경로일 경우, 비즈니스 로직 처리 
        }
~~~