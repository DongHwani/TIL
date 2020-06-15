# Refactoring(1)

오래된 레거시 프로젝트를 맡으면서 조금씩이나마 리팩토링한 경험을 정리한 글이다. 
 
현재 맡고 있는 프로젝트에는 VO 클래스가 존재하지 않는다. Map 구조를 확장하여 Custom하게 만든 Map 자료구조로 Controller에서 모든 데이터를 받아서 처리하고 있다. 

이렇게 구성되어 있다보니 데이터에 어떤 값이 들어가 있는지 확인하기가 굉장히 어려웠다. 또한 Vo가 없다보니 모든 비즈니스 로직들과 도메인 로직들이 Service 레이어에 다 꾸겨져 넣어있어 어떤 일들을 하는지 쉽게 알아보기 힘들었다. 

이 부분을 조금씩 개선하기 위해 아래의 세가지 스텝으로 코드를 리팩토링 해나아갔다. 

Step01. 책임의 경계선 파악
Step02. 모듈화 및 테스트
Step03. 모듈화된 기능 도메인 레이어에 구성

### Step01. 책임의 경계선을 파악

Service 레이어에 하나의 메서드에 약 200줄도 넘는 코드들이 굉장히 많았다. 또한 변수명들이 종종  `String x`이렇게 알아보기 어려운 구조로 되어있는 곳이 꽤나 많았다.  

우선, Debug모드를 통해 장황하게 이어져있는 코드들에서 경계선을 파악해 나아가기 시작했다. 

~~~JAVA
public void updateInformation(SuperMap map){
    
    
    String x = security.encodePassword(map.get("password"));
    map.put(x, "password" );
    
    //기타 비즈니로직 들. .. 

    String phoneNumber = map.get("x");
    phoneNumber = phoneNumber.replaceAll("-", "");
    map.put("phoneNumber", phoneNumber);
}
~~~
보통 실무에서는 위보다 더 알아보기 힘든 구조로 비즈니스로직들이 구성되어있다. 위는 설명을 위해 아주 간단한 예시로 들었다. 현재 `updateInformation` 메서드 내부에서 `패스워드 암호화`와 `핸드폰 번호 파싱`하는 부분으로 책임의 경계선을 나눌 수 있다. 



### Step02. 모둘화 및 테스트
마음 같아서는 Vo(Domain) 클래스를 바로 만들고 싶지만, 복잡한 로직과 현재 운영되는 서비스이기에 이는 너무 큰 위험부담을 갖게 된다고 생각하였다. 
 `패스워드 암호화`와 `핸드폰 번호 파싱`는 부분을 Service 레이어에서 따로 private 메서드를 만들어서 그 기능을 수행하고 테스트하기 시작했다. Map 구조로 받는 방식과 Vo가 없다는 점에서 아직 마음에는 안들지만, 운영되는 서비스이기에 당장 VO를 만들어서 다른 레이어를 만드는 것보다는 Service 레이어에서 개선하는게 운영환경에서 좀 더 안정적인 부분이라 판단이 되었다. 

~~~JAVA
public void updateInformation(SuperMap map){
    
    //패스워드 암호화 하는 부분
    
    String x = securityEncodePassword(map.get("password"));
    map.put("phoneNumber", x);
    
    //핸드폰 번호 파싱하는 부분 
    String phoneNumber = parsePhoneNumber(map.get("x"));
    map.put("phoneNumber", phoneNumber)
}
//테스트를 위해 public으로
public String securityEncodePassword(String rowPassword){
    return security.encodePassword(rowPassword);
}

public String parsePhoneNumber(String rowPhoneNumber){
   return rowPhoneNumber.replaceAll("-", "");
}
////////////////////////

@Test
public void pasrsePhoneNumer(){
    //Given
    String number = '010-1234-1234'

    //When
    String result = service.parsePhoneNumber(number);

    //Then
    assertEquals(result, number);
}
~~~

updateInformation이 가지고 있는 여러 책임들 중에서 모듈화가 가능한 부분을 메서드화 하여 구성하였다. 
동작이 제대로 수행되는지 확인하기 위하여 테스트는 필수 였다. 테스트가 통과가 완벽하게 된다면, 그 이후에 VO(Domain 레이어)를 만들 수 있었다. 


### Step03. 모듈화된 기능 도메인 레이어에 구성
