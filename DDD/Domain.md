# Domain

### 서론

최근 Domain과 객체지향 프로그래밍에 대해 다시금 생각해 볼 수 있는 일이 있었다. 이건 뒤에서 내 개인적인 생각을 정리해보기로 하고, 먼저 Domain이란 무엇인지 알아보자.  

(초입부분은 최범균님의 DDD START! 책을 참고하였다. )


### 1. Domain 이란? 
Domain이라는 단어는 프로그래밍을 하면서 굉장히 많이 들었던 단어이다. Domain이란 무엇일까? 

슈퍼마켓을 연상해보자. 슈퍼마켓에는 카테고리별로 다양한 상품을 판매한다. 특정 상품이 소진 될 경우, 마켓에서는 재고상품을 파악 후 물량확보를 위하여 입고 요청을 하기도 한다.

여기서 `슈퍼마켓`이 소프트웨어 관점에서 해결해야 할 `Domain`인 것이다. 또한 슈퍼마켓에서 `판매`, `주문`, 혹은 멤버십 가입자에 한하여 포인트 적립을 하는 `멤버십` 등 다양한 하위 도메인 모델들이 존재한다. 

![Domain](/DDD/img/Domain.png)


이렇게 논리적인 관점과 소프트웨어 측면에서 해결해야할 `어떠한 것(슈퍼마켓  처럼)`을 도메인이라 부른다. 



### 2. 객체지향스럽지 않은 Domain 구성에 대한 개인적인 경험담

여기서부터는 개인적인 경험담을 바탕으로 객체지향 프로그래밍으로써의 Domain에 대한 정리한 글임으로 가볍게 봐주길 바란다.

 최근 새로운 파견지로 근무 배정을 받고 인수인계 과정에서 조금은 충격적인 코드를 봤었었다.

예제
~~~Java
@Controller
public class SampleController {

    @RequestMapping
    public void addMember(HttpRequest request){
        SuperMap map = new SuperMap();
        Map<String, String> map = map.getMap(request);
    }
}
~~~

어떤 SuperMap(이라는 자체 클래스를 만들어서 Map구조로 Request를 처리하는 형식이었다. 

 종종 SI에서 Map형태로 Request를 바인딩 한다는 걸 들은 적이 있었다. 그리고 왜 Map으로 처리하면 안좋은지에 대해서도 개발선배들의 의견과 인터넷 커뮤니티 사이트에서도 본적이 있었다. 그 때는 다 이해하지 못하여, 아~ 이건 그냥 객체지향적이지 못해!라는 이유로 넘겨버렸지만, 단 하루만에 100% 이해 할 수 있는 계기가 되었다. 

위에 코드를 다시 살펴보자.  

 메서드 부분에서 `addMember`를 통해 이게 회원가입을 하는구나~ 라고 추측은 할 수 있다. 그런데 어플리케이션 레벨에서 `Member`라는 Class는 어디에도 존재하지 않았다. 또한, 도대체 Request가 어떤 값으로 구성되어있는지 `코드레벨에서 알 길이 없었다.` 

Member 뿐만 아니라 다른 부분에서도 죄다 Map으로 처리하다보니 현재 근무자인 그 사람 머리속에만 `개념적인 모델`이 있을 뿐, 인수인계를 받는 나는 쉽게 이해를 하지 못하였다.

 (참고로, 무작정 지금의 코드를 비난하는 건 아니다. 시스템은 오래됬으며, 이전 혹은 이전전 근무자들이 있었던 환경과 지금의 환경은 다를 수 있기 때문이다. )

 단순히 Member라는 개념적 모델을 하나만 사용하기에 단순하여 이해하기 쉬울순 있다. 그런데 만약, 여러 객체가 의존관계를 맺는 구조를 Map형태로 표현하면 쉽게 이해 할 수 있을까?

주문이라는 도메인을 예로 들어보자. 

 ~~~JAVA
public class Order {

    private Long orderId;
    private String orderCategory;
    private Member orderer;
    private Product product;
}
public class Member{

    private Long memberId;
    private String memberName;
    private String memberPassword;
    private Address address;

}

public class Product{

    private Long productId;
    private Long productName;
    private double price;

}
 ~~~
주문 객체에는 주문자인 Member와 상품인 Product가 있다. 이렇게 객체로 표현화하여 응집도를 높이는 방식과 

~~~Java
 Stirng orderId;
 String orderCategory;
 String memberId;
 String memberName;
 String memberPassword;
 String productId;
 String productName;
 String price;
~~~
위의 파라미터 값을 그대로 `request.getParameter`를 통해 Map 구조로 넣는 방식을 비교해보자.

order, member, product라는 구분을 지을 수 있게 변수명을 지었기에 구분이 가지만, price가 어디에 속하는지는 알 수가 없다. Service와 DAO 혹은 Mybatis의 경우 Mapper를 뒤져봐야 저 Price가 어떤식으로 사용하는지 알 수가 있다. price가 개념적인 모델로 어딘가에 속해져있어야 쉽게 이해할 수 있을 것이다. 그렇지 않기 때문에 Map구조 형태는 혼란을 줄 수 있는 대목이다.


### 정리

개념적인 모델들을 객체로 묶어 응집도를 높이고 서로 다른 객체 간의 상호작용하여 하나의 Domain의 문제를 해결하는 것은 중요하다. 객체지향프로그래밍에서 객체지향답지 못하게 Domain을 해결하면 응집도를 떨어트리고 해당 코드들이 무엇을 해결하는지 알지 못하는 미궁 속으로 빠질 수 있다.

앞으로 다가올 내 업무를 어떻게 해결해야 할 지 고민 좀 해봐야겠다.





 


