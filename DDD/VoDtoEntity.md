# Vo, Dto, Entity에 대한 개인적인 정리 

### Vo / Dto / Entity란 ?

Vo : 단순한 값 속성을 가지고 있는 객체

Dto : layer간 데이터 교환을 위한 객체

Entity : 고유의 식별성을 가지고 있는 객체


처음에는 Entity를 Jpa기술을 사용할 때만 쓰는 용어인줄 알았다. @Entity라는 어노테이션이 붙기 때문에 Entity = Jpa라고 헷갈릴 수 도 있는데 Jpa에 국한되는 객체가 아니다. persistence 프레임워크가 Mybatis로 되어있어도 Entity를 구성할 수 있다. Entity는 기술에 종속적인 객체가 아니라 **POJO 객체에 해당**된다.

Entity는 id라는 고유한 식별성을 가지고 있는 객체를 일컫기 때문에 다른 id를 가진 객체와 완전 다른 객체로 생각하면 된다. 

반면에 VO의 경우 **id가 없는 값 객체이다.**

아래의 예제를 통해 비교해보겠다. 

~~~JAVA

@Entity
@Getter @Builder
@Table(schema = "base", name = "members")
public class Member extends CreatedAndModifiedEntity {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long memberId;
    private String memberName;
	private String memberPassword;

	@Embedded
	private Address memberAddress;


~~~
(여기서 어노테이션은 신경쓰지말자. 필드구성을 확인해보자)


Member라는 Entity는 고유의 식별성을 가지는 Entity이다. 홍길동이라는 memberName을 가지고 있는 Entity가 있다고 생각해보자. memberId는 1번이다. 일종의 주민등록번호라고 생각할 수 있다. 주민등록번호는 한 사람을 지칭하고 식별하기 위해 나타낸 값이다. 주민등록번호의 특징은 **주민등록번호를 절대 바꿀수는 없는 반면에 이름은 개명할 수 있다.**

이처럼 1번이라는 Id값은 바뀌진 않지만 memberName이 기존에 홍길동에서 홍길순으로 바껴도 동일한 사람이란걸 우리는 Id를 통해 식별할 수 있다. 

이게 바로 Entity이다. 

이번에는 member entity에 있는 address 객체를 살펴보겠다.

~~~ JAVA
@Embeddable
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor(access = AccessLevel.PRIVATE)
public class Address {

    private String street;
    private String city;
    private String zipCode;
}

~~~

Address는 주거지 도로명, 도시명, 우편명으로 구성되어있다. 이 Address 객체는 식별성을 갖지 않으며 일종의 **값(Value)로써 역할을 한다.** (Member Entity의 한 field 일부로써)

마지막으로 Dto를 살펴보자. 

Dto는 Data Transfer Object의 약자이다. **계층간 데이터 교환을 위한 객체로 사용된다.**

일반적으로 Dto는 Controller와 Service 레이어간 데이터 교환을 위해 사용된다. 

내가 경험하고 느꼈던 Dto는 클라이언트와 밀접하기 때문에 클라이언트의 Request, Response에 요구되는 데이터에 맞게끔 Dto를 구성하였다. 

생각해보자. 어플리케이션 서비스는 클라이언트에게 **보여주는 데이터**와 **받는 데이터**로 나뉘어있다.  

위의 Member Entity에서 password는 클라이언트로부터 input값으로 받지만 ouput으로 response를 할 시에는 보안유출이 되어버린다. 

이렇게 Request, Response에 해당되는 데이터값을 Dto로 구성하고 Dto는 Entity로 컨버터로 변환하여 Application 레이어로 변환된 entity을 전달한다.


Dto객체는 Client에 대응되는 데이터만 다루는 객체이기 때문에 코어에 가까운 Entity객체를 다루는 Application Layer영역에 Dto가 넘어가면 안된다.

어플리케이션 서비스에 대한 요구사항은 항상 변하기 때문에 Client에 해당되는 인풋 아웃풋 값은 비즈니스 로직을 담고있는 Entity와 다른 성질로 변화하기 때문이다. 

