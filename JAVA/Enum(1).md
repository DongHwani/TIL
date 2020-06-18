# Enum 

Effective Java2의 Enum편을 보고 간략하게 정리한 글이다. 

Enum은 Java에서 안전한 상수 타입을 제공한다. Enum을 사용하기 전에 보통 상수 처리는 
아래와 같이 `static final` 키워드를 붙이거나 좋은 방법은 아니지만 인터페이스에서 상수를 구현하는 것이다. 

~~~java
public interface Brand{
    int BRAND_A = 7;
    int BRAND_B = 8;
}

public class Brand{
    public static final int BRAND_A = 7;
    public static final String BRAND_B = "BRAND_B";
}
~~~

위의 두 방식 모두 몇가지 문제점이 있다. 인터페이스 방식은 해당 인터페이스의 존재여부를 알아야만 사용할 수 있다. 실제로 이전에 어떤 프로젝트에서 상수처리하는 인터페이스가 있는지도 모르고 비슷한 상수를 다른 클래스에서 만들어 사용한 경험이 있다. 이렇게 시간이 지나다보면 결국 관리가 잘 되지 않는 채 인터페이스의 상수값들과 클래스 별로 선언된 상수값들이 혼재되어 혼란을 야기시킬 수 있다. 

두번 째인 static final 키워드를 붙여서 int형이나 String으로 처리하는 방식은 형 안전성을 보장하지 못한다. 오타를 내거나 이상한 값들과 비교한다거나 했을 때 `컴파일`시점에서 걸러낼 수 있는 방법이 없기 때문에 런타임 때 요상한 오류를 낼 수 있는 여지를 남겨둔다. 

Enum은 하나의 Type으로써 관리가 됨으로 컴파일 시점에서 형 안전성을 보장해준다. 

~~~JAVA

public enum Brand {

    BRAND_A(7), BRAND_B(8);

    private final int code;

    Naming(int code){
        this.code = code;
    }
    public int getCode(){
        return code;
    }

}
~~~

Enum 클래스 내부에 열거된 값들은 마치 하나의 싱글톤 객체처럼 사용된다. 이렇게 정적으로 상수처리 뿐만 아니라 메서드 구현도 가능하다. 예를 들어, 브랜드별로 할인율 적용이 다를 경우 아래와 같이 구현이 가능하다. 


~~~JAVA

public enum Brand {

    BRAND_A(7){
        public BigDecimal calculate(int price, double discountRate){
            return BigDecimal.valueOf(price * discountRate);
        }
    };

    private final int code;

    public abstract BigDecimal calculate(int pirce, double y);

    Brand(int code){
        this.code = code;
    }

}
~~~

Enum을 잘 다루지 못하여 구현하기 쉬운쪽으로 코드를 작성하게 된다. 물론 쉽게 구현하게 되면 그 때 그 순간만큼은 빠르게 해결해 나아갈 수 있다. 하지만, 컴파일 시점에서의 `형 안전성`을 고려하고 오류를 발견할 수 있는 Enum 장점은 그만큼 강력한 것 같다. 또한, 상수별로 메서드의 기능을 수행함으로써, 높은 추상화를 제공할 수 있는 이점도 있다. 





