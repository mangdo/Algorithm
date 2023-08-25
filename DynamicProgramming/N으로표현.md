# N으로 표현 🧮
> 프로그래머스 레벨 3, 코딩테스트 고득점 Kit <br>
> https://school.programmers.co.kr/learn/courses/30/lessons/42895


<br>

## 🧮 문제 조건
- N과 사칙연산과 괄호만을 이용해서 number를 표현 할 수 있는 방법 중 N 사용 횟수의 최솟값은?
- 1 <= N <= 9
- 1 <= number <= 32,000
- 수식에는 괄호와 사칙연산만 가능하며 나누기 연산에서 나머지는 무시한다.
- 최솟값이 8보다 크면 -1을 return 한다.


<br>

## 🧮 예제

### [ 예제1 ]
```
N: 5
number: 12
```

12 = (55 + 5) / 5 => 4번 사용 <br>
12 = (55 / 5) + (5 / 5) => 5번 사용 <br>
12 = 5 + 5 + (5/5) + (5/5) => 6번 사용 <br>
<br> 




<br>

## 🧮 문제 아이디어
1. N을 사용하는 최솟값이 8보다 -1 return
    - N을 8번 이상 사용할 일이 없음
    - N을 1~8번 사용하는 전체 케이스를 계산해도 충분히 가능
    - 비슷한 문제인 [타겟 넘버](https://school.programmers.co.kr/learn/courses/30/lessons/43165)에서도 주어진 숫자에 가능한 연산을 하나씩 다해서 답을 구했음! (최대 연산 횟수가 크지 않기 때문에 가능)
2. N을 사용해서 만들 수 있는 수 전체를 알아내자
    - **dp[1]: N을 1번 사용해서 만들 수 있는 수**
    - dp[2]: N을 2번 사용해서 만들 수 있는 수
    - dp[3]: N을 3번 사용해서 만들 수 있는 수..
3. dp[n]와 dp[n+1]의 관계
    - 노가다로 직접 해보면서 알아내보자.. (아래 참고)


- dp[1]
    - 5
- dp[2]
    - 55
    - 5+5, 5-5, 5//5, 5*5
- dp[3]
    - 555
    - dp[2] (연산) dp[1]
        - (5+5)+5, (5+5)-5, (5+5)//5, (5+5)*5
        - (5-5)+5, (5-5)-5, (5-5)//5, (5-5)*5
        - (5//5)+5, (5//5)-5, (5//5)//5, (5//5)*5
        - (5 * 5)+5, (5 * 5)-5, (5 * 5)//5, (5 * 5)*5
        - 55+5, 55-5, 55//5, 55*5
    - dp[1] (연산) dp[2]
        - 5+(5+5), 5-(5+5), 5//(5+5), 5*(5+5)
        - ...
- dp[4]
    - 5555
    - dp[2] (연산) dp[2]
        - (5+5)+(5+5), (5+5)-(5+5), (5+5)//(5+5), (5+5) * (5+5)
        - (5-5)+(5-5), (5-5)-(5-5), (5-5)//(5-5), (5-5)*(5-5)
        - (5//5)+(5//5), (5//5)-(5//5), (5//5)//(5//5), (5//5)*(5//5)
        - (5 * 5)+(5 * 5), (5 * 5)-(5 * 5), (5 * 5)//(5 * 5), (5 * 5)*(5 * 5)
        - 55 + 55, 55 - 55, 55 // 55, 55 * 55
    - dp[1] (연산) dp[3]
        - 5 + ((5+5)+5), 5 - ((5+5)+5), 5 // ((5+5)+5), 5 * ((5+5)+5)
        - 5 + ((5+5)-5), 5 - ((5+5)-5), 5 // ((5+5)-5), 5 * ((5+5)-5)
        - 5 / (55+5)
        - ...
    - dp[3] (연산) dp[1]
        - (55+5) // 5 = 12
        - ...
- dp[5]
    - 55555
    - dp[1] (연산) dp[4]
    - dp[2] (연산) dp[3]
    - dp[3] (연산) dp[2]
        - (55 // 5) + (5 // 5) = 12
    - dp[4] (연산) dp[1]

- dp[6]
    - 555555
    - dp[1] (연산) dp[5]
        - 5 + (5 + (5 / 5) + (5 / 5)) = 12
    - dp[2] (연산) dp[4]
    - dp[3] (연산) dp[3]
    - dp[4] (연산) dp[2]
    - dp[5] (연산) dp[1]



<br>
<br>

## 🧮 코드

```python
def get_all_operation(op1, op2):
    
    new_op = set()
    new_op.add(op1 + op2)
    new_op.add(op1 - op2)
    # 분모가 0일시 에러 발생
    if op2 != 0:
        new_op.add(op1 // op2)
    new_op.add(op1 * op2)
    
    return new_op

def solution(N, number):
    
    dp = []
    dp.append({N}) # 5

    for i in range(8):
        tmp = set()
        # 5, 55, 555 ...
        # string.rjust() : 원하는 문자, 문자열의 앞을 채워 원하는 문자열길이로 만든다
        tmp.add(int( str(N).rjust(i+2, str(N)) ))

        for j in range(i+1):
            for op1 in dp[j]:
                for op2 in dp[-j-1]:
                    tmp.update(get_all_operation(op1, op2))
        
        dp.append(tmp)

    
    # N을 8번까지 이용해서 다 만들고나서, 존재하는지 확인
    answer = -1
    for i in range(len(dp)):
        if number in dp[i]:
            answer = i+1
            break
    
    return answer

```

## 🪨 리팩토링 후의 코드
```python
def get_all_operation(op1, op2):
    new_op = set()
    
    new_op.add(op1 + op2)
    new_op.add(op1 - op2)
    if op2 != 0:
        new_op.add(op1 // op2)
    new_op.add(op1 * op2)
    
    return new_op

def solution(N, number):
    answer = -1
    
    dp = []
    for i in range(1, 9):
        all_case = set()
        # 5, 55, 555
        all_case.add(int(str(N) * i))
        
        # i=1일때는 돌아가지 않아서 {5}만 채워지게끔
        for j in range(0, i-1):
            for op1 in dp[j]:
                for op2 in dp[-j-1]:
                    all_case.update(get_all_operation(op1, op2))

        # N을 8번까지 이용해서 다 만들고나서 확인하지말고 그때마다 확인하게끔
        if number in all_case:
            answer = i
            break
        else:
            dp.append(all_case)
    return answer
```