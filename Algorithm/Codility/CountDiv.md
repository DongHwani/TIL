
[CountDiv](https://app.codility.com/programmers/lessons/5-prefix_sums/count_div/)

### 문제 
CountDiv 문제는 A와 B사이의 수 에서 K로 나눌 수 있는 정수의 수를 반환하는 문제이다.

예를 들어, A = 6, B = 11 , K = 2가 인자값으로 주어질 때, 결과값은 3이 나와야 한다.
6(A)과 11(B)사이의 수 중에서 2(K)로 나누어지는 수는 6, 8, 10 이기 때문이다. 


### 문제 복기

아래와 같은 식을 세워 문제를 풀었다. 
![countdiv](/Algorithm/img/countdiv.png)  

(0부터 B까지의 K로 나눈 총 수) - (0부터 A까지의 K로 나눈 총 수) 

그런데 여기서 위의 식을 사용 했을 경우, A 자신을 포함한 수가 나누어 떨어진 수에 해당되면, `+1`을 해줘야 한다. 

~~~JAVA
  public int solution(int A, int B, int K) {

            if (A % K == 0)
                return  (B / K) - (A / K) + 1;

            return (B / K) - (A / K);
    }

~~~

어떠한 수가 주어지더라도 단 한번의 연산이 이루어지기에 O(1) 시간 복잡도를 가진다.