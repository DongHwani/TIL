
# Spring Data JPA의 @Modifying 사용시 주의사항


@Modifying 어노테이션은 @Query 어노테이션 사용 시에 DDL을 지원하기 위하여 사용되는데 문제는 Persistence Context를 무시하고 DB에 값을 넣는다는 점이다.


### 1. 상황

특정 컬럼만 update를 하기 위하여 Spring Data JPA의 @Query와 @Modifying 어노테이션을 사용하여,
DB에 업데이트를 시도하였다. 

~~~JAVA
    @Modifying
    @Query(value="UPDATE Member m SET m.memberName = :#{#member.memberName} " +
            "                       , m.memberEmail =:#{#member.memberEmail}" +
            "                       , m.memberAddress =:#{#member.memberAddress} WHERE m.memberId = :#{#member.memberId}")
    int updateMemberInformation(@Param("member") Member member);
~~~


~~~JAVA
    @Test
    @Transactional(transactionManager = "jpaTransactionManager")
    @Rollback(false)
    public void memberUpdateInformation() throws Exception{
          //Given
        Member member = Member.builder()
                .memberPassword("123")
                .memberName("NotUpdate")
                .memberEmail("NotUpdate@memberUpdate.com")
                .memberSex("남")
                .memberAge(99)
                .roles(Arrays.asList("ADMIN"))
                .memberAddress("서울시 동작구")
                .memberPhoneNumber("010-7788-010")
                .accountEnable(accountEnable)
                .memberGrade("admin")
                .build();
        Member result = memberCommonRepository.save(member);

        MemberDto memberUpdate = MemberDto.builder()
                .memberName("Update")
                .memberEmail("update@memberUpdate.com")
                .memberAddress("서울시 노량진")
                .build();


        //When & Then
        mockMvc.perform(put("/api/member/{memberId}", result.getMemberId())
                .contentType(MediaType.APPLICATION_JSON_UTF8)
                .accept(MediaType.APPLICATION_JSON_UTF8)
                .content(objectMapper.writeValueAsString(memberUpdate)))
                .andDo(print())
                .andExpect(jsonPath("memberName").value(memberUpdate.getMemberName()))
                .andExpect(jsonPath("memberEmail").value(memberUpdate.getMemberEmail()))
                .andExpect(jsonPath("memberAddress").value(memberUpdate.getMemberAddress()))
                .andExpect(status().isOk());

~~~

- "member" 인스턴스를 DB에 먼저 insert한다. 
- "memberDto" 인스턴스에 변경하고 싶은 내용을 넣은 후, 비즈니스로직을 태운다.
- controller에서 정상적으로 내용이 변경되었으면, JSON 형태로 변경된 응답값을 전달 해준다. 

![test](/JPA/img/@Modifying.png)

위의 테스트 시나리오는 성공한다. 


Persistence Context에 어떤 내용이 들어가 있는지 확인하기 위해 테스트코드를 추가
~~~JAVA
    @Test
    @Transactional(transactionManager = "jpaTransactionManager")
    @Rollback(false)
    public void memberUpdateInformation() throws Exception {
        
        //생략...

        Optional<Member> contextTest = memberCommonRepository.findById(result.getMemberId());
        Member memberTest = contextTest.get();

        Assert.assertEquals(memberUpdate.getMemberName(), memberTest.getMemberName());
        Assert.assertEquals(memberUpdate.getMemberEmail(), memberTest.getMemberEmail());
        Assert.assertEquals(memberUpdate.getMemberAddress(), memberTest.getMemberAddress());
    }
~~~

테스트를 디버깅해서 실행하면 memberTest 인스턴스에는 DB에 변경되지 않는 값이 들어온다. 

![test](/JPA/img/@Modifying_context.png)

### 2. 해결법
@Modifying 어노테이션에는 이러한 문제를 해결하기 Persistence Context를 관리하기 위한 옵션을 제공한다.

~~~JAVA
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD, ElementType.ANNOTATION_TYPE})
@Documented
public @interface Modifying {
    boolean flushAutomatically() default false;

    boolean clearAutomatically() default false;
}
~~~

여기서는 clearAutomatically를 사용하는 방법이다.

clearAutomatically는 Query를 실행 한 후에 **PersisContext의 내용을 완전히 비우는 것이다.** 
~~~JAVA
    @Modifying(clearAutomatically = true)
    @Query(value="UPDATE Member m SET m.memberName = :#{#member.memberName} " +
            "                       , m.memberEmail =:#{#member.memberEmail}" +
            "                       , m.memberAddress =:#{#member.memberAddress} WHERE m.memberId = :#{#member.memberId}")
    int updateMemberInformation(@Param("member") Member member);
~~~
이렇게 선언해주면 해당 테스트는 통과한다.

하지만 단점이라면, PersistContext를 완전히 비우기 때문에, findById로 내용을 조회할 경우, DB 자원을 사용한다는 점인 것 같다.

간략하게 드는 생각은, 별로 좋은 방법은 아닌 것 같다. 매번 Persistenc Context를 지우고 하면 성능 저하로 이어질 수도 있을 것 같다. 

