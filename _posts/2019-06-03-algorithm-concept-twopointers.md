--- 
layout: single
classes: wide
title: "[Algorithm 개념] 투 포인터(Two Pointers), 슬라이딩 윈도우(Sliding Window) 알고리즘"
header:
  overlay_image: /img/algorithm-bg.jpg
excerpt: '투 포인터와 슬라이딩 윈도우를 사용해서 1차원 배열의 원하는 값을 찾아보자'
author: "window_for_sun"
header-style: text
categories :
  - Algorithm
tags:
  - Algorithm
  - Two Pointers
  - Sliding Window
use_math : true
---  

## 투 포인터와 슬라이딩 윈도우
- 투 포인터와 슬라이딩 윈도우는 $O(N^2)$ 이상 해결해야 하는 문제를 선형시간 $O(N)$에 해결 가능하게 한다.
- 두 알고리즘은 완전히 같은 알고리즘은 아니지만 설계적으로 매우 비슷한 알고리즘이다.

## 투 포인터 알고리즘
- 투 포인터 알고리즘은 1차원 배열에서 값들을 이용해 목표에 맞는 값(M)을 찾아주는 알고리즘이다.
- 각 값들은 자연수 이면서 목표에 맞는 값(M) 또한 자연수 이어야 한다.

## 투 포인터 알고리즘의 원리
- 배열에서 사용할 2개의 포인터 s(start), e(end) 를 준비한다.
- 초기 s = e = 0 에서 시작해서, 항상 s <= e 의 조건을 만족해야 한다.
- s 의 칸은 포함되고, e 의 같은 포함되지 않으며 `[s,e)` , s = e 는 아무것도 포함되지 않는 상태이다.
- s의 값이 배열의 크기(N), s < N 까지 반복하며 아래의 조건에 따라 진행하게 된다.
	- s-e 부분합이 M 이상 이거나, e = N 일경우 -> s++
	- 위의 경우가 아니라면 -> e++
	- s-e 부분합이 M과 같으면 -> 결과 카운트++, s++
	
## 예시
- 10개의 원소를 가진 아래와 같은 배열이 있다.
- 배열의 부분합이 같은 부분합(구간합)의 개수를 구한다.

	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	value|1|1|2|3|5|1|6|2|2|2| 

- M(부분합) = 6 이고, 초기 s와 e는 아래와 같다.

	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point|s,e| | | | | | | | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|0| | | | | | | | | | 


- 첫 번째 결과 카운트가 증가하는 부분까지 진행해 보면 아래와 같다.

	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point|s|e| | | | | | | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|1| | | | | | | | | | 
	
	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point|s| |e| | | | | | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|2| | | | | | | | | | 
	
	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point|s| | |e| | | | | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|4| | | | | | | | | | 

- s-e 구간의 합 sum의 값이 M보다 작기 때문에 계속해서 e 를 앞으로 진행 시킨다.

	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point|s| | | |e| | | | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|7| | | | | | | | | | 

- sum 의 값이 M 보다 크기 때문에 s 를 한칸 앞으로 진행 시키게되면 sum = M 이 만족 되었다.
	- 결과 값 카운트를 증가시키고 result = 1, s를 다시 한칸 더 앞으로 진행 시킨다.

	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| |s| | |e| | | | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|6| | | | | | | | | | 
	
	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | |s| |e| | | | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|5| | | | | | | | | | 
	
	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | |s| | |e| | | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|10| | | | | | | | | | 
	
	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | | |s| |e| | | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|8| | | | | | | | | | 
	
	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | | | |s|e| | | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|5| | | | | | | | | | 

- s-e 구간의 합인 sum = M 이므로 결과 카운트 증가 result = 2 및, s를 앞으로 한칸 진행시킨다. 

	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | | | |s| |e| | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|6| | | | | | | | | | 
	
	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | | | | |s|e| | | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|1| | | | | | | | | | 
	
	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | | | | |s| |e| | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|7| | | | | | | | | | 

- s-e 구간의 합인 sum = M 이므로 결과 카운트 증가 result = 3 및, s를 앞으로 한칸 진행시킨다. 

	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | | | | | |s|e| | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|6| | | | | | | | | | 

- s = e 인상황도 나올수 있다. e 앞으로 한 칸 진행

	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | | | | | | |s,e| | | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|0| | | | | | | | | | 
	
	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | | | | | | |s|e| | 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|2| | | | | | | | |
	
	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | | | | | | |s| |e| 
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|4| | | | | | | | | | 

- s-e 구간의 합인 sum = M 이므로 결과 카운트 증가 result = 4 시키고, 종료한다.

	idx|0|1|2|3|4|5|6|7|8|9|EOA
	---|---|---|---|---|---|---|---|---|---|---|---
	point| | | | | | | |s| | |e
	value|1|1|2|3|5|1|6|2|2|2| 
	sum|6| | | | | | | | |

- 주어진 배열에서 M = 6 을 만족시키는 부분합(구간의 합)의 개수는 4개임을 확인 할 수 있다.
- 투 포인터 알고리즘은 매 루프마다 두 포인터 중 하나의 포인트의 인덱스가 1씩 증가하고 배열에 끝에 가게되면 종료 되므로, 시간 복잡도는 $O(N)$ 이 된다.

- 소스코드

```java
public class Main {
    private int result;
    private int M;
    private int[] array;

    public Main() {
        this.array = new int[]{
                1, 1, 2, 3, 5, 1, 6, 2, 2, 2
        };

        this.M = 6;
        this.solution();
        System.out.println(this.result);
    }

    public static void main(String[] args) {
        Main main = new Main();
    }

    public void solution() {
        int start = 0, end = 0, sum = 0, len = this.array.length;

        while(true) {
            if(sum > this.M) {
                // start-end 구간의 합이 M 보다 크면 start 인덱스 증가 및 구간합에서 빼기
                sum -= this.array[start++];
            } else if(end == len){
                // end 인덱스가 배열 의 마지막 크기이면 루프 종료
                break;
            } else {
                // 둘아 아닐경우 end 인덱스 증가 및 구간합 더하기
                sum += this.array[end++];
            }

            // 구간합이 같다면 결과 카운트 증가
            if(sum == this.M) {
                this.result++;
            }
        }
    }
}
```  

---
## Reference
[투 포인터(Two Pointers Algorithm), 슬라이딩 윈도우(Sliding Window)](https://m.blog.naver.com/kks227/220795165570)  
[17. 투 포인터, 슬라이딩 윈도우 (1)](https://code0xff.tistory.com/128)  
