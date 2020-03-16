
# EnhancedRandom


기존 테스트에서 직접 객체에 할당하는 관용구 코드가 넘쳐나서 github의 EnhancedRandom이라는 라이브러리를 사용하여 객체에 Random값을 넣었다.


1. Refactoring 과정
- 기존코드
~~~JAVA
public class LectureRepositoryTest {
    //.....
    private Lecture lecture;

    @Before
    public void setUp(){
       lecture = createLecture();
    }
  
    @Test
    public void saveLecture(){
        //Given & When
        Lecture savedLecture = lectureRepository.save(lecture);

        //Then
        assertThat(savedLecture).isNotNull();
    }

    private Lecture createLecture() {
        List<LectureLine> list = new ArrayList<>();
        list.add(new LectureLine("제1장 객체", "내용"));
        list.add(new LectureLine("제2장 타입", "내용"));

        Member member = createMember();

        Lecture lecture = Lecture.builder()
                .lectureInformation("이 강의는 유익한 강의이다.")
                .lectureClassName("열혈 JAVA")
                .category(LectureCategory.JAVA)
                .price(new BigDecimal(150000))
                .instructor(member)
                .lectureLines(list)
                .build();

        return lecture;
    }

    private Member createMember() {
        Account account = Account.builder()
                .accountNonExpired(true)
                .accountNonLocked(true)
                .credentialsNonExpired(true)
                .build();
        Member member = Member.builder()
                .memberEmail("test@naver.com")
                .account(account)
                .nickName("닉네임")
                .phoneNumber("01001001")
                .password("password")
                .build();
        return memberRepository.save(member);
    }
}
~~~
private 메서드에 Lecture 클래스를 save하기 전까지 정말 많은 객체에 값을 넣고 생성해야하는 관용구 코드로 넘쳐났었다. 

또한, 클래스 내부에 value 타입으로 선언된 객체에 대한 테스트가 가장 큰 문제였다. 

Member 인스턴스에 Account 객체를 보면 Builder 패턴을 통해 생성하는 것을 볼 수 있다. Value 타입은 불변객체로 구성하여 외부의 변경이 안되도록 하는게 좋은데 여기서는 테스트를 위해서 이 부분을 일시적으로 허용하였다. 

만약 다시 원상복구한다는 것을 깜빡한다면 도메인 로직이 깨질 수 있는 문제를 낳게 된다. 

첫번째, 넘쳐나는 관용구 코드   
두번째, 테스트를 위한 도메인 로직의 변경 문제

이 두가지 문제를 해결하기 위해 github의 EnhancedRandom 라이브러리를 사용하였다.

- EnhancedRandom 라이브러리 적용
~~~JAVA
public class LectureRepositoryTest {

    //.....

    private Lecture lecture;

    @Before
    public void setUp(){
        lecture = EnhancedRandomBuilder.aNewEnhancedRandom().nextObject(Lecture.class, "lectureId");
        memberRepository.save(lecture.getInstructor());
    }


    @Test
    public void saveLecture(){
        //Given & When
        Lecture savedLecture = lectureRepository.save(lecture);

        //Them
        assertThat(savedLecture).isNotNull();
    
    }
~~~
@Before 어노테이션이 수행되는 setUp메서드를 보면 EnhancedRandom 라이브러리를 사용하여 Lecture 클래스에 값을 랜덤으로 넣을 수 있다. 

이전의 테스트와 비교 했을 때, Lecture를 save 시키기 위하여 Member객체와 entity에 속한 value타입들을 생성 해야하는 불필요한 긴 코드들이 많이 없어졌고, 도메인 로직을 테스트를 하기위하여 변경할 필요도 없어졌다.


![Debug](/TEST/img/Debug.png)

위의 테스트를 실행해서 디버그모드로 확인해 보면 Lecture 객체에 랜덤값이 들어간 걸 확인 할 수 있다. 

이렇게 특정 기능 혹은 도메인에 대해 테스트를 하고 싶을 때, 불필요한 객체 설정을 제거하기 위하여 라이브러리를 사용하는 것도 나쁘지 않은것 같다. 





