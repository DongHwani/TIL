# 버블 정렬

인접한 두 요소를 비교하여 정렬하는 알고리즘

O(n^2) 시간복잡도를 가지고 있기 때문에 상당히 느리나, 구현이 쉽다.


### 버블 정렬 동작과정

![버블정렬](/Algorithm/img/BubbleSort.png)  

위의 예제는 오름차순 과정이다. 0번째 인덱스부터 인접한(현 위치의 +1 인덱스)와 비교하여 정렬해 나아간다. 비교할 대상이 없는 시점까지 갔거나 혹은 인접한 인덱스의 숫자보다 자신이 작을 경우 멈춘다. 

1회전의 경우 53번이 인접한 인덱스보다 그 수가 계속 큼으로 맨 우측으로 정렬된다. 

2회전에서 13과 27은 이미 정렬이 되어있음으로, 정렬을 수행하지 않고 정렬이 되지 않는 31번부터 버블소트 정렬을 진행해 나아간다. 


[ Simple 예제 ]

~~~JAVA 
public class BubbleSortTutorial {

    static class Solution{
        public int[] solution(int[] bubble){

            //1번씩 회전할 때마다 맨 끝요소는 정렬이 완료된 상태임으로, 현재 크기에서 1씩 줄여간다.
            for (int i = bubble.length; i > 0; i--) {
                for (int j = 1; j < i; j++) {
                    //bubble[j-1]은 현재 요소이며, bubble[j]는 다음 요소이다.
                    //현재 요소가 다음 요소보다 클 경우 swap 해준다.
                    if (bubble[j - 1] > bubble[j]) {
                        int temp = bubble[j - 1];
                        bubble[j - 1] = bubble[j];
                        bubble[j] = temp;
                    }
                }
            }
            return bubble;
        }
    }

    public static void main(String[] args) {
        int[] bubble = {53, 13, 27, 31, 19, 20};
        Solution s = new Solution();
        int[] result = s.solution(bubble);

        for(int i=0;i<result.length;i++){
            System.out.print(result[i]+" ");
        }

    }
}

~~~

### 참고자료 

- [gmlwjd9405님의 블로그](https://gmlwjd9405.github.io/2018/05/06/algorithm-bubble-sort.html)

- [위키백과](https://ko.wikipedia.org/wiki/%EA%B1%B0%ED%92%88_%EC%A0%95%EB%A0%AC)