# Transactional의 readOnly 속성

### 1. Transactional readOnly 란?

readOnly true는 해당 트랜잭션은 **읽기 전용** 역할만 수행할 수 있도록 한다. 해당 트랜잭션에서 INSERT, UPDATE, DELETE와 같은 DB에 대한 쓰기방식을 쓸 경우에 오류가 난다.

선언적 트랜잭션인 @Transactional을 보면 readOnly의 속성이 false로 되어있다. 

~~~JAVA
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Transactional {
    //생략.....
    boolean readOnly() default false;
~~~

readOnly가 default로 false로 되어있어 INSERT나 UPDATE, DELETE 같은 DB 쓰기 작업들이 가능한 거였다.

그렇다면 Transactional의 readOnly의 true 속성은 언제 쓰는게 좋을까 ? 


### 2. Transactional readOnly true에 대해

1) 해당 메서드의 "읽기 전용" 역할만 수행하도록 보장
 만약 실수로 쓰기 작업을 하게 되면, 에러가 발생하기에 이를 보장하기 위하여 쓸 수도 있을 것 같다. 인터넷을 찾아보니 DB환경에 따라 readOnly 속성을 true로 할지라도 이를 무시하는 경우도 있다고 한다.
(이건 오래된 DB버전에 해당)

2) 하이버네이트에서의 최적화 ?
 FlushMode를 Manual로 변경하여 더티체킹 생략과 Datasource 커넥션 레벨에도 설정되어 약간의 최적화간 발생한다.
 (이 부분은 인프런의 어떤 분이 남긴 댓글에 대한 답변을 참고한 글)

실제로 Spring Data JPA의 구현체인 **SimpleJpaRepository**의 내부를 확인해 보면,

~~~JAVA
@Repository
@Transactional(
    readOnly = true
)
public class SimpleJpaRepository<T, ID> implements JpaRepositoryImplementation<T, ID> {
   // 생략....
}
~~~

@Transactional readOnly 속성이 클래스타입으로 true로 지정되어 있다. findyById 혹은 findByAll과 같은 메서드를 호출 할때 default로 readOnly의 true 속성을 사용하고 있다.

그 이외에도 save와 같은 쓰기 트랜잭션은 메서드 타입으로 선언하여 별도로 default 속성인 false를 사용하고 있었다.

~~~JAVA
    @Transactional
    public <S extends T> S save(S entity) {
        if (this.entityInformation.isNew(entity)) {
            this.em.persist(entity);
            return entity;
        } else {
            return this.em.merge(entity);
        }
    }
~~~

아직 언제 사용해야할지에 대해서는 공부를 더 해봐야 할 것 같다. 







