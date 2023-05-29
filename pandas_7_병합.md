# 데이터 프레임 병합

1. merge() : df 병합
- 공통 열: key
```ts
df1.merge(df2.how)
```
------
|how = ' '| |
|:--:|:--:|
|how = 'inner' | key 값 동일한 데이터만 포함(디폴트값)|
|how = 'outer' | 모든 값 포함 . 존재하지 않으면 NaN |
|how = 'left' | 왼쪽 기준 |
|how = 'right'| 오른쪽 기준(왼쪽 존재하지 않으면 NaN)|


## 조인 방식
- inner join : 공통 열의 동일 값만 포함
- outer join : df 모든 데이터 포함 (값이 없으면 NaN)

```ts
df1.merge(df2, how='inner')  # inner 생략 가능(디폴트)
pd.merge(df1, df2, how='inner')
```

### 데이터 프레임 키 값이 같은 여러 데이터
-> 가능한 모든 경우의 수 조합

## **기준열 명시**
### 두 df에서 이름이 같은 열은 모두 key (공통 열)// 키로 사용 못할 경우 --> 기준열 on 사용

- on = 기준열 이름
```ts
pd.merge(df1, df2, on='고객명', how='outer')
```

## 키가 되는 기준 열 이름 다른 경우

- left_on, right_on 사용
- 인덱스 사용 병합
    - 인덱스 기준 열로 병합(left_index = True / right_index = True)
    - 양쪽 df key 모두 인덱스 사용하는 경우 모두 설정
    - (left_index, right_index)
    - (left_on, right_index)
    - (left_index, right_on)

```ts
# 데이터프레임 생성
df1 = pd.DataFrame({
    '도시': ['서울','서울','서울','부산','부산'],
    '연도': [2000,2005,2010,2000,2005],
    '인구':[9853972,9762546,9631482,3655437,3512547]    
})


# 다중 인덱스로 생성
df2=pd.DataFrame(
    np.arange(12).reshape((6,2)),    # 12개의 값을 6행 2열로 생성   
    index=[['부산','부산','서울','서울','서울','서울'],
          [2000,2005,2000,2005,2010,2015]],   
    columns=['데이터1','데이터2']
)

# 계층적 인덱스(다중 인덱스) 사용 시 원소 접근
# . 연산자를 이용한 체인 인덱싱 (연속적)
df2.loc['부산'].loc[2000] # 특정 행 (모든 열 값 추출)

# 특정 행열 값 추출 : 서울 2015년 데이터1 추출
df2.loc['서울'].loc[2015]['데이터1'] # 10

# iloc 인덱서 사용 시
df2.iloc[5] # == df2.loc['서울'].loc[2015]
df2.iloc[5]['데이터1']  # == df2.loc['서울'].loc[2015]['데이터1'] # 10
```

## df 두개 인덱스 사용 병합
```ts
pd.merge(df1, df2, left_index=True, right_index=True, how='inner')

```

## join 메서드
- 기본으로 인덱스 기준 병합
- 양쪽 df key 가 모두 인덱스인 경우
- df1.join(df2, on=None, how='left', lsuffix='', rsuffix='', sort=False)
    - on : 기준 설정
    - lsuffix / rsffix : 동일한 열이름에 붙일 접미사
    - sort : index 정렬 여부
- 열 기준으로 병합

<!-- 인덱스 기준으로 join -->
```ts
df1 = pd.DataFrame(
[[1.,2.],[3.,4.],[5.,6.]],
index=['a','c','e'],
columns=['서울','부산'])
df1

df2=pd.DataFrame(
[[7.,8.],[9.,10.],[11.,12.],[13.,14.]],
    index=['b','c','d','e'],
columns=['대구','광주'])
df2

# 인덱스를 기준으로 join()
df1.join(df2, how='inner') # 기본 인덱스 기준

df1.join(df2, how='outer')


# 열 기준으로 join()
# set_index(열)을 사용하여 열을 인덱스로 변경한 후 join()
df1.set_index('고객명').join(df2.set_index('고객명'), on='고객명', lsuffix='_x', rsuffix='_y')
```

---
## concat() 메소드 -- 데이터 연결

## concat()
- 단순 데이터 연결
- axis=0 (디폴트) : 행 결합
- axis= 1 : 열 결합
    ```ts
    pd.concat([s1, s2], axis=0)
    pd.concat([s2, s1],  axis=1)
    # 입력한 시리즈 순대로 나옴
    ```
- keys 지정,다중 인덱스
    ```ts
    sc = pd.concat([s1, s2], keys=['x', 'y'])
    # a 추출 : loc 연속 사용
    sc.loc['x'].loc[1]
    # d,e 추출
    sc.loc['y'].loc[2:3]
    ## 0-base 위치 인덱스 x

    ```
---

## df연결
```ts
# 모든 열이 동일한 두 df 생성

df1 = pd.DataFrame(
    np.arange(6).reshape(3, 2), # 6개 값, 3행 2열
    index=['a', 'b', 'c'],
    columns = ['열1', '열2'])
df1

df2 = pd.DataFrame(
    5 + np.arange(4).reshape(2, 2), # 4개 값, 2행 2열
    index=['d', 'e'],
    columns = ['열1', '열2'])
df2 

# 단순 행 연결 : 행 추가 결과
pd.concat([df1, df2])
# 옆으로 연결 : 열 연결
pd.concat([df1, df2], axis=1)
```

### 열이 다를 때
- 행 연결
    - 기본 : 합집합 (join='outer')(전체연결) / 없는 곳은 NaN
    - 교집합 (join ='inner')
    ```ts
        pd.concat([df1, df2], axis=0, join='inner')
    ```
- 열 연결    
    ```ts
    pd.concat([df1, df2], axis=1)
    pd.concat([df1, df2], axis=1, join='inner') 
    pd.concat([df1, df2], axis=1, join='outer') 
    ```

##  다중 인덱스 원소 추출
```ts
df.loc['x'].loc[0, 'A']

# y의 0~2행, E F열의 값 추출
df.loc['y'].loc[0:2, 'E':'F']
# loc(행, 열) -> loc(:, :)

# 비연속 열 선택:[,] 사용
df.loc['z'].loc[1:2,['C', 'O']] 

# z의 비연속 행, 열 선택
df.loc['z'].loc[[1, 3],['C', 'O']]
# loc([], []) 
```


---
## 3개의 데이터프레임에서 공통적으로 나타나는 열만 추출
### join='inner' : 교집합
```ts
pd.concat([df1, df2, df3], join='inner', keys=['x', 'y', 'z'])
```

## ignore_index = True : 기존 인덱스 제거.
## 0-base 위치 인덱스로 설정됨
```ts
pd.concat([df1, df2, df3], join='inner', ignore_index=True)
```


