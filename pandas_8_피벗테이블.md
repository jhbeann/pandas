# 피벗테이블 : pivot_table()

```ts
pivot_table(data, values, index, columns, aggfun, fill_value, margins, margins_name)
```
- data : 분석할 df
- values : 분석할 열
- index : 행 인덱스로 들어갈 키열 또는 키열의 리스트
- columns : 열 인덱스로 들어갈 키열 또는 키열의 리스트
- aggfunc : 분석 메소드. mean(기본)
- fill_value : NaN 대체값 지정
- margins : 모든 데이터를 분석한 결과를 행열로 추가할 지 여부
- margins_name : 추가 margins 열(행) 이름

- 방법 : 두개의 키를 사용해서 데이터를 선택
(행 인덱스, 열 인덱스)



---

## 인수명 생략 시 기본 순서 
## pivot_table(데이터, 행 인덱스, 열 인덱스)
## - df1.pivot_table('인구', '도시', '연도')
### 결과 : 각 도시의 연도별 평균 인구수 출력


## 선실 등급에 따른 성별에 대해 생존여부별로 나이와 티켓값의 평균과 최대값을 산출
```ts
df_t = pd.pivot_table(df,  # 피벗할 df
                      values=['age', 'fare'],  # 계산할 데이터
                      index=['class', 'sex'],  # 행 인덱스
                      columns='survived',      # 열 인덱스
                      aggfunc=['mean', 'max'], # 적용 함수 (여러개 가능)
                     )
```

## 그룹 분석 : groupby()
- 그룹 객체 반환
- 그룹 객체에 대해 그룹 연산 수행

## Groupby 클래스 연산 메서드
|연산|            |
|:--:|:--:|
|size(),count()| 개수 반환|
|mean,median|평균, 중앙값|
|min,max|최대 최소|
|sum,prod()|합계, 곱|
|std,var|표준편차, 분산|
|quantile|사분위수|
|first,last()|첫번째,마지막 데이터 반환|
|agg(),aggregate()|원하는 함수 만들고 agg에 전달|
|describe()|여러개 값을 df로 반환|
|apply()|원하는 그룹이 없는 경우 사용|
|transform()| 그룹별 계산을 통해 데이터 변형|


----

## key1으로 그룹화
###  GroupBy 클래스 객체 반환
```ts
key1_groups = df2.groupby(df2.key1)

# 그룹화 확인
key1_groups.groups
# {'A': [0, 1, 4], 'B': [2, 3]} # 반환 형태 : 딕셔너리

# {그룹명 : 그룹에 포함된 행인덱스}

# 키 확인
key1_groups.groups.keys() 
# 반환 :  dict_keys(['A', 'B'])

# 각 그룹의 내용 확인
key1_groups.groups['A']

# 그룹화 객체를 df로
pd.DataFrame(key1_groups)

# 특정 그룹 선택 : get_group()
key1_groups.get_group('A')


```

