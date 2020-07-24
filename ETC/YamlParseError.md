Travis CI를 yml 파일로 설정하고 GitHub에 push하였는데 GitHub에는 push가 정상적으로 되었지만
Travis CI에서는 빌드 시도조차 하지 않는 현상이 발생했다. 

![couldnotparse](/ETC/img/CouldNotParse.png)

확인해보니 yml파일 문법에 맞지 않는 형식을 사용하고 있었다. 
~~~yaml
deploy:
 - provider: codedeploy
      access_key_id: $AWS_ACCESS_KEY
      secret_access_key: $AWS_SECRET_KEY
~~~

TAB키를 사용하여 access_key_id랑 secret_access_key를 나열했는데 이 부분에서 parse 오류가 났었다. 

(yaml 혹은 yml 파일과 관련해서 `TAB`키는 많은 오류를 나올 수 있어 지양한다는 인터넷 글들이 꽤 있었다..) 

이유인 즉 `-` 표시자가 의미하는 바는 일종의 field 단위로 생각하면 되는데, yaml 양식에서는  `-` 표시자 하위의 field를 둘 수가 없다. yaml 파일 특성상 TAB 키로 데이터 간의 계층구조를 표시하기 때문에 위와 같은 에러가 발생한 것이다. 

아래와 같이 올바른 표현양식으로 고치니 바로 빌드가 성공했다. 

~~~yaml
deploy:
 - provider: codedeploy
   access_key_id: $AWS_ACCESS_KEY
   secret_access_key: $AWS_SECRET_KEY
~~~


위의 양식은 json양식으로 표현하면 아래와 같음
~~~yaml 
deploy : {
          provider: codedeploy, 
          access_key_id: $AWS_ACCESS_KEY,         
          secret_access_key: $AWS_SECRET_KEY
          }
~~~
