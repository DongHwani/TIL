# HashMap(1)

hash에 대한 정리와 Java에서의 HashMap 자료구조에 대해 정리한다. 

해당 정리한 내용은 Java11버전 기준으로 정리하였다. 분량조절 실패로 여기서는 Hash의 필요성과, 
HashMap의 전체적인 자료구조 형태, 그리고 Put하는 과정에 대해서만 정리한다. 

(혼자 공부하고 정리한 내용이기에 틀린 내용이 많을 수 있다.)


### 1. hash값은 왜 필요할까? 

좀 억지스러운 설정일 수도 있지만, 어떤 10장의 포커 카드가 있다고 가정하자. 그 포커카드를 잘 섞어 책상에 뒷면이 보이도록 10장의 카드를 가로로 나열하였다. 이 10장의 카드에서 내가 찾고자 하는 카드는 '스페이스 7'이 적힌 카드를 찾으려고 한다. 한번에 하나의 카드만 확인이 가능하다는 설정이 있었을 때, 첫번째 카드부터 마지막 열번째카드까지 스페이스 7이 나올 때까지 확인하는 방법이 있다. 

10장밖에 안되서 하나씩 확인하는 것은 많은 시간을 소요하진 않지만, 만약 100장 혹은 1000장의 카드를 확인하게 되면 많은 시간을 소요하게 될 것이다. (운이 좋아서 내가 원하는 값이 첫번째 카드일 수도 있지만, 항상 보장하지 않는다는게 문제이다.)

만약, 일종의 꼼수를 사용해서 뒷면에 내가 원하는 카드를 식별할 수 있도록 특정값이 있다면 어떨까? 스페이스 7을 지칭할 수 있는 353이라는 숫자가 적혀 있다면, 처음부터 찾을 필요없이 353이라는 숫자를 보고, 단 한번에 내가 원하는 숫자를 찾을 수 있다. 

프로그래밍 관점에서 봤을때, 이런 상황에서 사용되는 자료구조는 `Map`이다. 여기서  
Key, Value형태로 자료구조가 저장되는데, Key값은 `해싱함수`라는 것을 사용하여 Key에 대응되는 `해시값`을 만든다. 이런 Key에 대응되는 `해시값`을 사용하여 빠르게 원하는 값을 찾을 수 있다. 

(위에서 스페이스 7을 지칭하는 353이 일종의 해시값이라고도 볼 수 있다.) 

![HashFunction](/Algorithm/img/HashFunction.png)  

### 2. 전체적인 HashMap 구조

![HashMap](/Algorithm/img/HashMap.png)  

전체적인 HashMap의 구조이다. `Buckets` 이라는 용어가 나오는데 버킷은 HashMap의 배열 요소이며, 하나의 버킷에 두개 이상의 `Node` 혹은 `Entry`라는 자료를 담을 수 있다. 

HashMap 자료를 put할 경우 Key, Value 값 두개만 넣는데 그림을 보면 4개나 들어가 있다.
~~~JAVA
HashMap<String, String> map = new HashMap<>();
//난 Key, Value만 넣었는데 왠 갑자기 Null값?
map.put("test", "테스트");
~~~

Key값에 대응하는 Hashcode를 한번에 찾아서 원하는 값을 가져오기에 bucket에 hashcode가 들어가는건 이해가 간다. Null값에 대해서는 뒤에 Java코드를 살펴보면서 다루고 여기서는 우선 잊자.

 위 그림을 정리하면, HashMap에 Key와 Value값을 넣을 때 Key값이 
해시함수를 통해 `해싱`되어 `hashcode`가 만들어지고, HashMap의 캡슐화된 내부 동작방식에 의하여 `Bucket`이라는 곳에 데이터가 hashcode, Key, Value, 그리고 null이 저장되는 것을 알 수가 있다.

이제 어떻게 데이터가 위의 그림의 구조로 bucket에 들어가는지 확인해보자. 

### 3. HashMap Put 과정

어떤 스포츠선수를 표현한 Player라는 객체가 있다고 가정하겠다. 이 객체에는 그 선수의 이름과 스포츠종목을 담는 변수가 있다. 

~~~JAVA

public class Player {
    private String name;
    private Sports sports;

    public Player(String name, Sports sports) {
        this.name = name;
        this.sports = sports;
    }

    //Getter 생략...
    @Override
    public boolean equals(Object obj) {
        if(obj==null) return false;
        if (!(obj instanceof Player)) return false;
        if (obj == this) return true;
        Player player = (Player) obj;
        return (name.equals(player.name))
                && (sports.getValue() == player.getSports().getValue());
    }

  @Override
    public int hashCode() {
        int result = 17;
        result = 31 * result + sports.hashCode();
        return result;
    }
}

public enum Sports {
    BASEBALL(0), SOCCER(1), BASKETBALL(2);

    int value;
    Sports(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }
}
~~~
hashcode는 sports 종목이 같으면 동일한 Hashcode가 나오도록 로직을 구성하였다. 여기서 hashcode가 일부로 동일하게 나오게 한 부분은 hash 충돌이 일어났을 때 HashMap에서 어떤 일이 벌어지는지 확인하기 위해 위와 같은 로직을 구성하였다. (hashcode는 같더라도 다른 객체임을 어떻게 HashMap에서 인식하는지를 알아보기 위함)

Put과정에 대한 상세로직 정리는 아래의 순으로 정리한다. 

 - HashMap 생성후 처음 Put 과정
 - HashCode가 다른 두번째 Put 과정
 - HashCode가 동일한 세번째 Put 과정
 
**첫번째, HashMap 생성후 처음 Put 과정** 
~~~JAVA
    @Test
    public void hashMapPut(){
        HashMap<Player, String> map = new HashMap<>();

        map.put(new Player("Son", Sports.SOCCER), "공격수"); //첫번째, Put
    
    }

~~~
![HashMapProcess](/Algorithm/img/HashMapProcess01.png)

put을 하면, hash함수를 통해 hashcode를 얻고나서 `putVal`이라는 메서드에서 구체적으로 HashMap의 자료구조를 넣는 과정이 진행된다. 
(위의 putVal 메서드 부분은 너무 길어서 밑에 일부 생략된 사진으로 넣었다.)

이제 여기서 구체적인 자료구조 put과정인 `putVal` 메서드를 살펴보겠다. 
![HashMapProcess02](/Algorithm/img/HashMapProcess02.png/)

첫번째 분기문인 if문에서 `this.table` 변수를 확인해 볼 수 있다. 이 필드 부분에 타입을 보면 HashMap 내부의 Static Class로 선언된 `Node` 클래스타입의 배열형태로 선언되어있다. Node의 필드 구성을 살펴보면, `hash`, `key`, `value`, `HashMap.Node`로 구성되어있는데, 아까 처음으로 봤던 그림의 HashMap의 Bucket에 저장된 구조가 이 `HashMap.Node`임을 알 수있다. 여기서 `null`값은 `next`변수인 HashMap.Node를 뜻한다. 

그래서 하나의 Buket에 여러 `HashMap.Node`들이 연결된 구조로 데이터가 쌓아 나아갈 수 있다. 
![HashMapExam](/Algorithm/img/HashMapExam.png/)


다시 돌아와, 우리는 HashMap자료구조에 처음으로 put하였기 때문에 `this.table`이 가르키는 Node 배열에는 어떤한 값도 없어 `n = (tab = this.resize()).length;` 이 로직을 수행하게 된다. 
여기서 `resize()` 메서드를 통해 `HashMap.Node`의 `배열 사이즈`를 얻을 수 있다. `resize()`메서드가 `HashMap.Node[]의 사이즈`를 초기화하는 과정에서 내부로직은 `16` 사이즈로 고정되어 값을 리턴한다. 



내용이 너무 길어 지금 `resize()`를 수행 한 부분까지 내용을 그림으로 표현하면 아래와 같다. 

![HashMapProcess03](/Algorithm/img/HashMapProcess03.png)

잠시 헷갈릴 수 있는 부분은 HashMap의 사이즈 크기이다. 그러면 HashMap의 사이즈는 초기화 할 때 무조건 16인가? 라고 생각 할 수 있지만, 아니다. 여기서의 사이즈는 HashMap.Node[]타입의 `table` 변수 배열에 대한 `버킷의 사이즈`이다. HashMap의 실질적인 사이즈는 물리적인 자료구조로 구하는 것이 아닌 HashMap내부의 int size 필드를 두어 remove, clear 혹은 put 등 이렇게 자료가 추가되거나 삭제되는 메서드 내부에서 ++size , --size 를 구현하여 size를 측정하고 있다.


간단히 정리하면 지금까지는 HashMap.Node[] table을 초기화한 과정이다. 이제 이 table에 어떻게 자료를 넣는지 살펴보자.

![HashMapProcess04](/Algorithm/img/HashMapProcess04.png)

HashMap 자료구조는 입력순서에 대한 순서를 보장하지 않는다. 위의 사진에서 비트연산을 통해 특정 index를 구하여 지역변수인 `tab`에 값을 넣는 것을 볼 수있다.

여기서 tab과 table이 같은 데이터를 참조하고 있어, tab에 값을 넣으면 table에도 값이 동일하게 들어간다. 


![HashMapProcess05](/Algorithm/img/첫번째Put.png)


**두번째, HashCode가 다른 두번째 Put 과정**

~~~JAVA

    @Test
    public void hashMapPut(){
        HashMap<Player, String> map = new HashMap<>();
        map.put(new Player("Son", Sports.SOCCER), "공격수"); //첫번째, Put
        map.put(new Player("RYU", Sports.BASEBALL), "투수"); //두번째, Put
    }
~~~

두번째 값을 Put할 경우, 처음 Put한 프로세서와 거의 동일하게 동작한다.
~~~JAVA
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
        HashMap.Node[] tab;
        int n;
        
        if ((tab = this.table) == null || (n = tab.length) == 0) {
            n = (tab = this.resize()).length; //두번째 put은 이 부분을 수행하지 않는다.
        }
        //생략...
    
        if ((p = tab[i = n - 1 & hash]) == null) {
            tab[i] = this.newNode(hash, key, value, (HashMap.Node)null);
        } else {    
        //생략.....
        }
    }
~~~
`resize()` 메서드를 수행하지 않는 점만 제외하면 첫번째와 동일하게 두번째 Put은 동작한다.

![두번째Put](/Algorithm/img/두번째Put.png)


**HashCode가 동일한 세번째 Put 과정**

~~~JAVA
    @Test
    public void hashMapPut(){
        HashMap<Player, String> map = new HashMap<>();
        map.put(new Player("Son", Sports.SOCCER), "공격수"); //첫번째, Put
        map.put(new Player("RYU", Sports.BASEBALL), "투수"); //두번째, Put
        map.put(new Player("Choo", Sports.BASEBALL), "타자"); //세번째, Put
    }
~~~

이번에는 동일한 HashCode를 갖고 있을 때, Put의 동작과정을 살펴보겠다. 


![세번째Put](/Algorithm/img/세번째Put.png)

hashcode가 동일하지만, 다른 객체일 경우 기존의 hash값이 존재하는 Node 객체의 필드인 `next` 변수에 새로운 객체의 값을 넣는다. 

이렇게 다른 객체지만 동일한 hash값을 가질 경우 마치 LinkedList 형태로 Key 값들을 관리하게 된다. 해시충돌 해결법으로 `Chaining(체이닝)`이라고 부른다.

### 정리
여기까지 Put과정에 대해 정리를 마친다. 계속 알아보면 알아볼 수록 Put만 하더라도 너무 양이 방대하여(실은 나의 실력이 부족하여) 빙산의 일각만 정리하고 전체적인 HashMap에 대한 이해를 중심으로 공부하였다. 

다음에는 Get, Remove를 할 때 HashMap에서 어떤일들이 벌어지는지 정리해봐야겠다. 

[Reference]  
- [geeksforgeeks](https://www.geeksforgeeks.org/internal-working-of-hashmap-java/)
- [NaverD2](https://d2.naver.com/helloworld/831311)
