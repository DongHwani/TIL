### GrantedAuthority와 Role의 차이

이 둘의 차이가 약간 헷갈리는 것 같아서 정리하게 되었다.

사전적으로 해당 단어를 먼저 해석해보겠다. 

Role은 역할을 뜻하며, GrantedAuthority는 승인된 권한(?) 정도로 해석 되는 것 같다. 

역할(Role)이 가지는 의미는 Application 내에서 계정이 가진 역할이 무엇인지를 의미한다. 
Ex) 사용자(User), 관리자(Admin) 등...

GrantedAuthority는 스프링 시큐리티에서는 역할(Role)이 가지고 있는 **개별적 권한** 혹은 Authentication(인증) 객체에 **부여된 권한**이라고 생각하면 될 것 같다. 

Ex)READ_AUTHORITY, WRITE, READ_ONLY 등.. 

풀어 설명하자면, User의 역할을 가진 A라는 사용자는 자신이 게재한 글에 대한 **수정할 수 있는 권한**이 있고,다른 사용자들은 **읽을 수 있는 권한**만 있다. 

여기서 읽을 수 있는 권한과 수정 할 수 있는 권한이 GrantedAuthority가 된다. 


[Reference]

[Baeldung](https://www.baeldung.com/spring-security-granted-authority-vs-role)

[Spring Security 공식문서](https://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#tech-granted-authority)

 