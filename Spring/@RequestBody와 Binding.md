
### ModelBinding

Controller 파라미터에 @RequestBody 어노테이션을 사용하여 Http 요청으로부터 오는 내용을
Java 객체로 역직렬화하여 바인딩한다. 

~~~JAVA
 @RequestMapping("/member")
    public ResponseEntity saveMember(@RequestBody Member member) {
            //생략..
    }
~~~
~~~JAVA
    @Test
    public void model_binding_test() throws Exception{
        //Given
        Member member= Member.builder()
                .memberName("KDH")
                .phoneNumber("0102222333")
                .address("Seoul")
                .build();

        //When & Then
        mockMvc.perform(post("/member")
                .contentType(MediaType.APPLICATION_JSON)
                .accept(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(member)))
                .andDo(print())
~~~

Member객체를 json형태로 post로 controller에게 보내면, body에 json형태로 담긴 객체에 대한 내용을
Controller에 있는 Member객체의 필드에 대입하여 바인딩된다. 

바인딩되는 기준은 Body의 Key값과 Controller에 있는 객체의 필드명이 일치하면 객체가 바인딩이 완료된다. 

~~~shell
MockHttpServletRequest:
      HTTP Method = POST
      Request URI = /member
       Parameters = {}
          Headers = [Content-Type:"application/json;charset=UTF-8", Accept:"application/json", Content-Length:"81"]
             Body = {"memberId":null,"memberName":"KDH","phoneNumber":"0102222333","address":"Seoul"}
    Session Attrs = {}
~~~

~~~JAVA
@Entity @Getter
@AllArgsConstructor(access = AccessLevel.PRIVATE)
@Builder
public class Member {

    @Id @GeneratedValue
    private Long memberId;
    private String memberName;
    private String phoneNumber;
    private String address;

}
~~~

mock request에 body내용에 있는 key값이 Member 클래스에 있는 필드가 일치한 걸 볼 수 있다.


Model Binding과 관련된 기술중에는 @RequestBody이외에도 @ModelAttribute가 있다. 이 둘의 차이점은 @RequestBody는 Setter가 없어서 바인딩이 가능하고, @ModelAttribute는 key값 일치와 Setter 메서드가 있어야 객체 바인딩이 가능하다는 것이다.

@ModelAttribute의 Setter가 필요한 제약조건은 외부로부터 무분별한 변경을 허용하기 때문에 Setter가 없이도 사용가능한 @RequestBody를 주로 사용한다.


내부적으로는 HttpMessageConverter가 @RequestBody에 명시된 객체로 역직렬화한다.







