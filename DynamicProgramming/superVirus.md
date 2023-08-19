# 바이러스 🦠
> 몸풀기 문제! <br>
> Softeer 난이도 2 <br>
> https://softeer.ai/practice/info.do?idx=1&eid=407

<br>


## 🦠 문제 조건

- 바이러스가 숙주의 몸속에서 1초당 P배씩 증가한다. (1 ≤ P ≤ 10^8)
- 처음 바이러스의 수 K마리가 있었다면, N초 후에는 총 몇 마리가 될까? (1 ≤ K ≤ 10^8, 1 ≤ N ≤ 10^6 )
- 최종 바이러스 개수를 1000000007로 나눈 나머지를 출력하라.

<br>

## 🦠 예제

### [ 예제1 ]

```
2 3 2
```

초기 바이러스 개수 * (증가율)^(총 시간)
2 * 3^2 % 1000000007 = 9

<br>

## 🦠 문제 아이디어

- 1 ≤ N ≤ 10^6, 1 ≤ P ≤ 10^8 라는 점을 유의해야한다
    - 그대로 수식을 작성할 시, 수가 너무 커져서 시간 초과 발생!
    - **% 1000000007을 스텝마다 반복**하면서 수가 너무 커지는 것을 방지한다.

<br>
<br>

## 코드
```python
import sys

initial_virus_count, increase_percent, total_time = map(int, input().split())

current = initial_virus_count
for _ in range(total_time):
    current = current * increase_percent % 1000000007

print(current)

```

<br>
<br>



# 🦠 수퍼 바이러스 🦠
> Softeer 난이도 3 <br>
> https://softeer.ai/practice/info.do?idx=1&eid=391

<br>


## 🦠 문제 조건 🦠

- 바이러스가 숙주의 몸속에서 **0.1초당 P배**씩 증가한다. (1 ≤ P ≤ 10^8)
- 처음 바이러스의 수 K마리가 있었다면, N초 후에는 총 몇 마리가 될까? (1 ≤ K ≤ 10^8, **1 ≤ N ≤ 10^16** )
- 최종 바이러스 개수를 1000000007로 나눈 나머지를 출력하라.
<br>


## 🦠 예제 🦠

### [ 예제1 ]
```
1 2 1
```

(초기 바이러스 수) * (증가율)^(총시간 * 10) % 1000000007 
<br>
= 1 * 2^10 % 1000000007
<br>
= 1024

<br>

## 🦠 문제 아이디어 🦠

- 1 ≤ N ≤ 10^16 라는 점을 유의해야한다
- 증가율이 3이고, 시간이 50이라고 예시를 들어보자.
    + 3^50 값을 빠르게 구하는 방법?
        + 3^50 
        + = (3^25) * (3^25)
        + = ((3^12) * (2^12) *3) * ((3^12) * (2^12) *3)
    + => **반복되는 작은 단위로 나눠서 계산**
    + => [DP의 Top Down방식](https://github.com/mangdo/TIL/blob/main/Algorithm/dynamicProgramming.md#-topdown)(memoization을 이용해서 한번 구한 계산 결과를 메모리 공간에 메모한다)

<br>

## 코드

```python

import sys

initial_virus, increase_percent, total_time = map(int, sys.stdin.readline().split())

# dynamic programming
# 탑다운 방식으로 저장
memo = dict()

def increase_virus(time):
    # 이미 계산 한 것이라면, 저장된 값을 꺼내서 쓰기
    if time in memo:
        return memo[time]
    
    
    # 계산 안한거라면, 계산하고 저장하기
    result = 0
    if time == 1:
        result = increase_percent
    elif time % 2 == 0:
        result = increase_virus(time//2) * increase_virus(time//2)
    else:
        result = increase_virus(time//2) * increase_virus(time//2) * increase_percent

    # 이전 몸풀기 문제 참고, 값이 너무 커지는 것을 방지
    result %= 1000000007
    memo[time] = result

    return result 

# 마지막에도 최종으로  나누기
print(increase_virus(10*total_time) * initial_virus % 1000000007) 

```