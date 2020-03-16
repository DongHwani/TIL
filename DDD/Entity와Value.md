# Entity, Value 


### 1. Entity, Value 개념 

**``Entity`` :** 식별자를 갖는 객체로 자료구조인 데이터와 그와 관련된 기능을 제공
~~~JAVA
public class Member{

    private Long memberId;
    private String memberName;

    //이름을 바꾸는 기능을 제공
    public void changeMemberName(final String newMemberName){
        this.memberName = newMemberName;
    }
}
~~~
위의 Member Entity는 memberId와 memberName이라는 데이터 구조와 changeMemberName이라는 메서드 기능을 제공한다. 

**`Value` :** 식별자가 없는 객체로 개념적으로 표현되며, 도메인 객체의 일부분인 특정 속성을 표현한다.

~~~JAVA
public class Member{

    private Long memberId;
    private String memberName;
    private Address address; // Value Type

    public void changeMemberName(final String newMemberName){
        this.memberName = newMemberName;
    }
}
~~~
~~~JAVA
public class Address{

    private String city;
    private String zipcode;
    private STring street;

}
~~~
Address 객체는 총 3개의 필드로 구성되어있다. 이 3개의 필드는 **주소**라는 하나의 개념적인 값 타입을 만들 수 있다. Address 객체는 Member 객체와 달리 식별가능한 id값이 없다. 


### 2. Entity, Value Type 로직(?) 구성

강의클래스 정보를 담고 있는 Lecture와 회차별 강의목록을 구성하고있는 LectureLine이 있다. 

~~~JAVA
public class Lecture  {

    private Long lectureId;

    private Member instructor;

    private String lectureClassName;
    private String lectureInformation;

    private LectureCategory category;
    private BigDecimal price;

    //강의 회차별 목록인 List타입의 LectureLine
    private List<LectureLine> lectureLines = new ArrayList<>();

~~~

~~~JAVA
public class LectureLine {

    private String lectureTitle;
    private String lectureContents;

}
~~~

여기서 LectureLine을 수정 해야하는 로직을 추가해보자. 예를 들어, 초급 Java 라는 강의 클래스가 있고, 그 클래스의 회차별 강의 목록은 총 3회차로 구성되어 있다.
여기서 3회차 강의를 한 회차를 폐강하여 2회차 강의 목록으로 바꿀려고 한다.

그렇다면 수정되는 강의목록(LectureLine)에 대한 로직은 어느 Class에 넣는게 좋을까? 

Entity인 id값이 있는 `Lecture`에 기능을 구성해야한다. 

~~~JAVA
public class Lecture  {

    private Long lectureId;

    //생략....

    private List<LectureLine> lectureLines = new ArrayList<>();

    //강의 목록 update 로직 
    public void updateLectureLines(List<LectureLine> newLectureLines) {
        if (newLectureLines == null || newLectureLines.size() == 0) {
            throw new IllegalArgumentException();
        }
        this.lectureLines = newLectureLines;
    }
~~~

만약, LectureLine 클래스에 위의 update 로직이 있다고 가정해보면 `Lecture` 도메인 기능과 구성이 깨질 수 있는 문제의 소지를 만들 수 있다.

 Lecture 클래스가 DRAFT된 상태에서는 강의목록(LectureLine)을 수정할 수 없다고 가정해보자. 도메인 수정에 대한 변경점이 `Lecture`에 접근해서 수정되는 것이 아니라 Lecture entity를 건너뛰고 외부에서 LectureLines으로 바로 접근해서 수정하여 상태가 DRAFT일지라도 **변경이 허용**되는 문제가 발생한다. 

이 점을 고려하여 도메인 로직을 구성할 때에는 Value Type은 가급적 불변으로 구현하고 도메인의 구성이 깨지지 않도록 Entity 내부에 설계하는 것이 좋을 것 같다.

또한, 개념적으로 중요한 점은 Entity는 그 자체적으로 생명주기를 가지고 있고, Value Type은 별도의 생명주기가 없이 소속된 Entity의 생명주기에 맞춰 같이 생성, 수정, 소멸이 된다는 점이다. 그래야만 id라는 식별성을 가진 entity의 도메인 구성이 깨지지 않기 때문이다. 
