# Value Type Collection

해당 내용은 `자바 ORM표준 JPA프로그래밍` 참고하여 작성함  

미니프로젝트를 진행 중에 Entity에서 Value 타입을 Collection 형태로 관리해야하는 상황이 생겼다. 


![값타입](/JPA/img/값타입01.png)


강의명이라는 Lecture Entity에 LectureLine이라는 Value 타입이 담겨져 있는 형태이다.

~~~JAVA
@Entity
public class Lecture extends BaseTime {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long lectureId;

    //..기타 필드 생략

    @ElementCollection(fetch = FetchType.EAGER)
    @CollectionTable(name = "lectureLines", joinColumns = @JoinColumn(name = "lectureId"))
    private List<LectureLine> lectureLines;

   //..생략
}

@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Embeddable
public class LectureLine {

    private String lectureTitle;
    private String lectureContents;

}
~~~

@ElementCollection과 @CollectionTable 이 두가지 어노테이션을 사용하면 Value타입의 Collection 객체를 사용할 수 있다. 

@CollectionTable의 속성값은 LectureLine객체의 테이블 속성 정의부분이다. `name=lectureLines`는 생성할 테이블 이름이고, `joinColumns`속성을 통해 자신이 속한 Entity의 PK키를 명시하였다.  


![값타입02](/JPA/img/값타입02.png)

관계형 데이터베이스에는 Value 타입 컬렉션을 관리할 LectureLine테이블이 생성된다. 


여기까지는 구현에 큰 문제가 없었다. 원하는 목적이었던 Value타입 객체가 별도의 생명주기를 갖는 것이 아닌 Entity에 귀속되어 Value로써의 역할만 하기를 원했기 때문이다. 
(Value 타입은 불변성을 유지하여 Entity에서 관리하는게 안전하다. 직접적인 외부로의 변경을 피하기 위해서다.)


하지만, 가장 큰 제약사항은 Entity객체는 식별성을 갖는 `ID`값을 가지고 있지만, 값객체는 ID값이 없어 RDB에 접근 후 데이터 변경이 어렵다는 것이다. 

그래서 Value타입에서 데이터가 변경시에 연관 데이터를 모두 삭제한 후에 Collection의 모든 값을 다시 저장한다. 
~~~JAVA
@Service
@RequiredArgsConstructor
@Transactional
public class LectureChanger {

    private final LectureRepository lectureRepository;

    public void updateLectureLines(final Long lectureId, List<LectureLine> updateLectureLines){
       Lecture lecture = lectureRepository.findById(lectureId)
                    .orElseThrow(() -> new IllegalArgumentException());

       lecture.updateLectureLines(updateLectureLines);
    }
}
~~~

~~~JAVA
    @Test
    public void LectureLinesUpdate() {
        //Given
        Lecture savedLecture = lectureRepository.save(lecture);

        List<LectureLine> updateLines = new ArrayList<>();
        updateLines.add(new LectureLine("제 1장 오리엔테이션", "내용"));
        updateLines.add(new LectureLine("제 2장 학습목표", "내용"));
        updateLines.add(new LectureLine("제 3장 객체", "내용"));

        //When
        lectureChanger.updateLectureLines(savedLecture.getLectureId(), updateLines);
        Optional<Lecture> optional = lectureRepository.findById(savedLecture.getLectureId());
        Lecture result = optional.get();

        //Then
        assertThat(result.getLectureLines(), hasSize(3));
    }
~~~

![값객체SQL결과](/JPA/img/SQL결과.png)

위의 테스트를 실행하면 delete 쿼리 한번과 변경된 LectureLine에 대한 3번의 insert가 날라간것을 확인해볼 수 있다. 

로직자체가 3개가 추가된 List자체를 바꿔서 insert가 3개 날라간것처럼 보이기도 한다. 영속성관리 객체에서 가지고 와서 기존 List에 add로 객체요소를 추가할 경우에도 List의 사이즈 만큼 Insert가 된다. 


### 정리
- 값객체는 불변성으로 만드는 것이 좋다. Entity의 생명주기에 의존하기 때문에 외부에서의 접근을 막아줘야 하기 때문이다. 안그럴 경우 도메인 로직이 깨질 수 있는 위험성을 낳는다.

- 값타입과 Entity 사용을 혼돈해서는 안된다. 위의 제약사항을 살펴봤듯이 Delete 쿼리와 List 사이즈만큼의 Insert 쿼리문이 수행되기 때문에 값타입이 꼭 필요할 때에만 사용해야한다. 



