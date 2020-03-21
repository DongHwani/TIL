# Dependency Injection(의존관계 역전 원칙)

### 의존관계 역전원칙(DIP)

 - 상위 모듈(고수준)은 하위 모듈(저수준)에 의존해서는 안된다. 상위 모듈과 하위 모듈 모두 추상화에 의존해야한다.
 - 추상화는 세부 사항에 의존해서는 안된다. 세부사항이 추상화에 의존해야한다.

위키 백과에 나온 DIP의 개념이다. 여기서 고수준, 저수준이라는 단어는 무엇을 의미하는 것일까?
또한, 추상화된 것이 세부사항에 의존해서는 안되고 세부사항이 추상화에 의존해야된다는 말은 무슨 의미일까? 

예제를 통해 이 고수준과 저수준이 추상화에 의존해야한다는 의미와 추상화와 세부사항의 의존도에 대해 살펴보겠다. 

어떤 자동차가 있다고 가정해보겠다. 이 자동차가 브레이크를 밟았을 때 타이어 종류에 따라 브레이크의 구현방식이 달라진다.

타이어의 종류는 슬릭타이어, 일반타이어가 있다고 가정하겠다.

~~~JAVA

//슬릭타이어를 사용하였을 때 브레이크 동작방식
public class Break {

    private SlickTier slickTier;
    private int breakDistance = 10;

    public void executeBreak(){
        slickTier = new SlickTier(breakDistance);
        slickTier.slickTierStop();
    }
}


public class SlickTier {

    private int slickBreakingDistance;

    public SlickTier(int breakDistance) {
        this.slickBreakingDistance = breakDistance;
    }

    public void slickTierStop(){
        System.out.println("SlickTier 제동거리 : "+ slickBreakingDistance);
    }

}
~~~

시간이 흘러 일반타이어로 교체해달라는 고객 요구사항이 왔다.
그러면 아래와 같이 Break 클래스를 변경해줘야한다. 

~~~JAVA
//노말타이어로 변경
public class Break {

    private NormalTier normalTier;
    private int breakDistance = 8;

    public void executeBreak(){
        NormalTier normalTier = new NormalTier(breakDistance);
        normalTier.normalTierStop();
    }
}
~~~


타이어 교체가 이루어질 경우, Break 클래스에 너무 많은 변경들이 일어난다. Break클래스는 자신이 `Break를 밟는 행위`에만 관심이 있지 어떤 타이어를 사용하든 Break 클래스는 알바가 아니다. 이는 Break클래스가 Tier 클래스에 너무 많은 의존도를 가지고 있기 때문이다. 

우선, 위의 예제는 `SRP원칙`인 단일책임원칙에 어긋난다. Break클래스가 구체적인 Tier의 제동거리를 알 필요는 없다. 
 
하나의 클래스에는 하나의 책임만 가져야 한다. SRP 원칙을 지킴으로써 의존도를 낮추어 클래스간 `결합도`는 낮추어지고 `응집도`는 높아질 수 있다. 

또한, 각 Tier클래스 별로 `멈추는 행위`를 표현하는 것은 동일하다. 중복되고 동일한 부분을 한단계 캡슐화하여 리팩토링 해보겠다.


~~~JAVA
public class Break {

    private Tier tier;

    public void executeBreak(){
        tier = new SlickTier(); //Normal타이어일 경우 tier = new Normal();
        tier.tierStop();
    }
}

public class NomalTier implements Tier {

    private int nomalBreakingDistance = 8;

    @Override
    public void tierStop() {
        System.out.println("NomalTier 제동거리 : " + nomalBreakingDistance);
    }
}
~~~
breakingDistance(제동거리)에 대한 속성은 각 Tier클래스가 가지게 되어, Break 클래스에서 구체적으로 알 필요가 없어졌다.그리고 
타이어의 `행위에 대한 표현은 동일`한 중복된 표현을 `인터페이스`로 한단계 추상화 하였다. 만약 여기서 다시 NormalTier로 바꿔달라는 요청이 들어 올 경우, 객체 생성부분만 바꿔주면 간단해진다.

Tier 인터페이스로 한번 더 감싸서 추상화하여 Break와 Tier간의 의존도를 낮췄지만, Break와 Tier간의 객체 생성시 `new` 키워드를 사용함으로써 의존도가 드러난다. 이 문제를 해결하기 위하여 `DIP(의존관계역전)` 방식으로 해결 할 수 있다. 


~~~JAVA
/**
 * 타이어와 브레이크의 관계설정은
 * 운전자가 대신한다
 */
public class Driver {

    public static void main(String[] args){
        Tier tier = new SlickTier();

        //브레이크 넌 알필요 없어, 타이어는 운전자인 내가 교체한다.
        Break carBreak = new Break(tier);

        carBreak.executeBreak();

    }
}

/**
 * Break클래스는 생성자 파라미터로 들어오는 Tier가
 * Normal인지 Slick타이어 인지 알 수가 없다
 */
public class Break {

    private Tier tier;

    public Break(Tier tier){
        this.tier = tier;
    }

    public void executeBreak(){
        tier.tierStop();
    }
}

~~~
`관계설정에 대한 책임`을 타 클래스의 책임으로 가지게 함으로써 기존의 Break클래스와 Tier클래스 간의 한층 더 의존관계를 낮출 수 있다. 

의존관계를 직접적으로 맺는 것이 아닌 타 클래스로 위임함으로써 관계를 역전한다는 의미로 `DIP(의존관계역전)` 법칙이라 한다. 

### 정리

DIP는 결국 인터페이스의 장점을 극대화 시키는 기술이라는 걸 느꼈다. 
(Abstract도 해당이 된다.)

이렇게 함으로써, 고수준인(Break를 밟는 행위)와 저수준인(각 Tier별로 제동하는 방식)을 분리하고 두 모듈다 추상화된 인터페이스에 의존되기 때문이다. 


[실습예제 링크]()

[Refference]
- [머루의개발블로그](http://wonwoo.ml/index.php/post/1717)
- 토비의 스프링 3.1



 
