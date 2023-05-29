# bigdata_pandas

##  파이썬의 주요 분석 라이브러리
- Numpy : 배열 , 행렬 관련 편리 기능
- Pandas : series, DataFrame
- Matplotlib : 시각화

# 1. pandas_series
## 판다스

### 1. series(1차원배열)
- index 와 value 일대일 대응 


### 2..  DataFrame (2차원 테이블)

---
## * pandas/numpy 설치 후 import

```ts
!pip install pandas
!pip install numpy

import pandas as pd   
import numpy as np
```

### 변수 명이 두번 이상 출력 되어도 콘솔에 등장

```ts
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity="all"
```

## Series 생성 방법 : pd.Series(집합적 자료형)
1) 리스트로 생성
    - 명시하지 않을 경우 0-base
    - list 내 서로 다른 타입 데이터 ( 문자형 포함 -> 문자형)
    - 정수+ 실수 -> 형태(dtype) : float
    - NaN 포함 => dtype:float
    - 인덱스 명시 (index 표기 생략 가능)
    ```ts
    s = pd.Series([1,2,3], index=[1,2,3])
    s = pd.Series([1,2,3], index=['a', 'b', 'c'])
    ```
    - 시리즈 이름/인덱스 이름 붙이기
    ```ts
    s.name = '성적'
    s.index.name='성명'
    ```
    - 시리즈의 값 : numpy array()형태 - 1차원 배열
    ```ts
    s.values
    ```
2) 튜플로 생성
    - pd.Series(,,)

3) 딕셔너리로 생성
    - Series({key:value,key1:value1....})  
    - key -> 인덱스
    - value -> 값
    ```ts
    city = {'서울':9631482,'부산':3393191,'인천':2632035,'대전':1490158}
    s_city = pd.Series(city)
    print(s_city)
    ```
4) range()/np.arange() 함수 사용
    - range() :: index: 0-base
    ```ts
    s = pd.Series(range(5))
    index/ value 모두 0~4
    ```
    - range(start,end) : start~ end-1 까지의 범위
    - numpy의 arange() : np.arange

5) 결측값 포함한 시리즈
    - 넘파이 배열 : nan
    - 판다스 시리즈 데이터프레임: NaN 
    - np.nan & None 을 NaN으로 인식

6) ### 데이터 추가와 변경   
   시리즈[index] = value   
   시리즈[index] = new value

7) 시리즈 내 원소 삭제 : del 사용
    - del s['인덱스이름']
    - del(s['인덱스이름'])

**주의**  --> 이미 삭제된 경우 다시 실행하면 에러 


* 인덱스 확인하기
```ts
s.index
s['인덱스이름']
s[0] : 첫번째 원소 값 출력 (0-base)
s[[2,1,0]] : 차례대로 시리즈로 출력
s2.a : 문자 인덱스인 경우 . (dot) 사용 가능
```
**주의** : 정수형 인덱스 => 0-base 위치 인덱스 사용 불가

## 슬라이싱
    (1) 0-base 이용한 슬라이싱  
        - 시리즈[start:end] : start ~ end-1 추출
    (2) 문자(라벨) 인덱스를 이용한 슬라이싱 
        - 시리즈['시작라벨':'끝라벨']
            - 시작에서 끝까지 양쪽 라벨 포함된 범위 추출  
    

## 시리즈 연산
(1) 벡터화 연산

    - 인덱스 값은 변경 불가
    - 시리즈의 값에 해당
    - 원소 각각에 대해 독립적으로 계산
(2) Boolean selection 

    - s[연산식] : 연산 결과 True값 추출
    - & ,| 사용
    -  인덱스 관계연산 가능 : s[s.index] > 5
    
(3) 두 시리즈간의 연산

    - 


