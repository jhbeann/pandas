# 응용예제2 _ CCTV와 인구수 데이터 분석

1. 서울시 각 구별 cctv 수를 파악하고 인구대비 cctv 비율
2. 고령자/외국인의 수가 cctv 설치에 영향을 줬는가?
3. 인구대비 cctv의 적정치를 확인하고 cctv가 과하게 부족한 구를 확인

- 사용 데이터 
- 서울시 지자체별 cctv 현황 data/CCTV_in_Seoul.csv
- 서울시 지자체별 인구 현황 data/population_in_Seoul_2023.xlsx

## 1. cctv data 읽어오기
- 주의 : CCTV 수 파악 => 연산 필요(증가율, 합계,...)
- , 포함 : 문자 ==> , 제거하고 숫자로 설정 
**<thousands= ','>**


```ts
cctv_seoul = pd.read_csv("../data/CCTV_in_Seoul.csv", thousands= ',', encoding='cp949')
cctv_seoul.info()
# NaN 없음
```
## 데이터 전처리
- 컬럼명 변경 df.reanme(columns={바꿀위치:새로운값})
- 불필요한 첫행 제거 : drop(위치, axis=0/1)
- 2013년도 이전 ~ 2019년까지 합해서 '2019년도 이전' 열로 추가
- 불필요한 열 삭제 : 2013년도 이전 ~ 2019년 삭제
- 열이름 순서 변경 : 구별, 2019년도 이전, 2020년, 2021년, 2022년
```ts
cctv_seoul.rename(columns={'구분': '구별',
                           '2013년 이전\n설치된 CCTV': '2013년도 이전',
                          }, inplace=True)
cctv_seoul.drop([0],inplace=True) 

cctv_seoul['2019년도 이전'] = cctv_seoul.loc[:, '2013년도 이전':'2019년'].sum(axis=1)

cctv_seoul.drop(cctv_seoul.columns[1:10], axis=1,  inplace=True)

cctv_seoul = cctv_seoul[['구별', '2019년도 이전', '2020년', '2021년', '2022년']]
```
## 전처리 결과 파일로 저장 : cctv_seoul_result1.csv
```ts
cctv_seoul.to_csv('../data/cctv_seoul_result1.csv', index=False)
# 저장한 데이터 다시 읽어와서 확인
cctv_seoul_result1 = pd.read_csv("../data/cctv_seoul_result1.csv")
```
## 가공 필드 생성
- 구별 cctv 수 확인 
- 소계 열 추가 : 구별 총 합계
- 증가율 열 추가 (2019년 이전보다 3년동안<2020,2021,2022>증가한 비율)
- 중구 이름 중간에 스페이스 포함 전처리
```ts
cctv_seoul.sum(axis=1)
cctv_seoul['소계'] = cctv_seoul.sum(axis=1)

cctv_seoul['증가율'] = cctv_seoul.loc[:,'2020년': '2022년'].sum(axis=1) / cctv_seoul['2019년도 이전'] * 100
cctv_seoul.rename(columns = {'증가율':'최근 증가율'}, inplace=True)
cctv_seoul.iloc[1, 0] = '중구'
```

----

# 서울시 인구현황 데이터
## 1) 데이터 가져오기
- 열 선택 : B(동별(2), J(계), K(한국인), L(등록외국인), N(65세이상고령자)) => usecols = ,,
```ts
pop_seoul = pd.read_excel('../data/population_in_Seoul_2023.xlsx',header=1,usecols= 'B, J, K, L, N')
```
## 2) 전처리
- 열 이름 변경 : 구별, 인구수, 한국인, 회국인, 고령자
- 첫행이 불필요한 데이터(합계) :drop
- 중복 없이 구별 개수 출력
- NaN 확인 : isnull()
```ts
pop_seoul.columns = ['구별', '인구수', '한국인', '외국인', '고령자']
# 데이터 정보 확인
pop_seoul.info()
pop_seoul.drop(0,inplace=True)
len(pop_seoul['구별'].unique())
pop_seoul[pop_seoul['구별'].isnull()]
# 동일 pop_seoul[pop_seoul['구별'].isna()]
```

### 인구 데이터 확인
```ts
# 인구 많은 자치구 : 내림차순 정렬
pop_seoul.sort_values(by='인구수', ascending=False).head()
# 고령자 많은 자치구 : 내림
pop_seoul.sort_values(by='고령자',ascending=False).head()
```


### 가공 필드 생성
- 외국인, 고령자 비율 추가
```ts
pop_seoul['외국인비율'] = pop_seoul.외국인 / pop_seoul.인구수 *100
pop_seoul['고령자비율'] = pop_seoul.고령자 / pop_seoul.인구수 *100
```

## 인구대비 cctv대수 적정성 확인
- cctv 데이터와 인구 데이터 병합
- 양쪽 df의 공통 열 '구별' 기준으로 병합
- df.merge(key='구별') : 공통열
- 불필요 열 삭제(소계 제외한 2022~2019년 이전 삭제)
- 구별 컬럼을 행 인덱스로 : set_index()
```ts
pop_seoul_result = pd.merge(cctv_seoul, pop_seoul, on='구별', how='outer')
pop_seoul_result.drop(pop_seoul_result.columns[1:5], axis=1, inplace=True)
pop_seoul_result.set_index('구별', inplace=True)
# cctv + pop
data_result = pop_seoul_result 
```
---
# 상관관계 분석

- 인구와 관련된 각 필드와 cctv 소계와의 상관계수를 파악해서 그래프로 표현
- 상관계수 : 두 변수의 관련성을 확인
- np.corrcoef(데이터값 1, 데이터값 2)
## 1. cctv vs 인구수
```ts
np.corrcoef(data_result['소계'], data_result['인구수'])
```
-  상관계수 : 0.47096459 : 양의 상관관계

```ts
!pip install scipy
import scipy.stats
scipy.stats.pearsonr(data_result['소계'], data_result['인구수'])
```
- 상관계수 : 0.470
- pvalue=0.017 <0.05 ==> 유의미하다
## 2. cctv vs 외국인 비율
```ts
np.corrcoef(data_result['소계'], data_result['외국인비율'])
```
-  상관계수 : -0.30397716. 약한 음의 상관관계

## 3. cctv vs 고령자 비율
```ts
np.corrcoef(data_result['소계'], data_result['고령자비율'])
```
- 상관계수 : -0.34216449. 약한 음의 상관관계

### **<결과>**
- cctv 대수와 인구수 간 상관계수가 외국인비율 또는 고령자비율 보다 약간 높음
- cctv 대수와 인구수 시각화 ==> 데이터간의 관계 파악 


## CCTV vs 인구 현황 시각화
```ts
# 그래프 패키지
import matplotlib.pyplot as plt 
%matplotlib inline 
# 노트북 내 out 창에서 그래프 출력
```
### 구별 cctv 수 확인 (수평막대 그래프)
```ts
data_result[['소계']].plot(kind='barh', grid=True)
plt.xlabel('cctv 대수')
plt.show()
```
# 소계를 기준으로 내림차순 정렬
```ts
data_result[['소계']].sort_values(by='소계').plot(kind='barh', grid=True)
```


## 분석 결과
1. cctv 대수는 강남구가 가장 많이 설치되어 있음
    - 그러면 강남구 가장 안전한가?
2. 인구데이터와 cctv 상관관계 확인했을 때 
    - 인구수 vs cctv : 보통의 양의 상관성
3. 인구수 vs cctv 관계 그래프 그리기

### 인구수 대비 cctv 설치 비율 계산/ 그래프
```ts
data_result['cctv 비율'] = data_result['소계'] / data_result['인구수'] * 100

data_result[['cctv 비율']].sort_values(by='cctv 비율').plot(kind='barh', grid=True)
plt.show()
```
### **<결과>**
### 인구대비 cctv 비율은  중구가 월등히 높음
- 주거지역이 적고 유동인구가 많은 대표 지역
- 성동구, 강남구, 종로구도 높은 편


## 인구수와 cctv 관계 확인(산점도) 
- scatter()
```ts
data_result[['인구수', 'cctv 비율']].plot(kind='scatter', 
                                     x='인구수', 
                                     y='cctv 비율'
                                    )
plt.title('인구수 대비 cctv 비율')
plt.show()
```
# data 저장
```ts
data_result.to_csv('../data/data_result.csv')
```


## 각 구별 인구수에 맞는 적정 cctv 대수 추정
- cctv와 인구와의 관계를 산점도로 표현 
- 관계 데이터를 기반으로 한 대표 직선(회귀직선)을 표현 : 최소 오차가 되는 직선 표시
    - (1) 최소 오차가 되는 회귀선 직접 생성해서 표시
    - (2) lmplot() 사용해서 회귀선 포시
- 각 구별 cctv 대수 추정하고 추정된 대수와의 차이 계산

### 회귀 함수 
- polyfit(x,y,차수)
- poly1d() : 다항식을 구하는 함수
- linspace() : 직선의 x, y값으로 사용할 값 생성 // linspace(stat, end, num개)

### 1. polyfit(x, y, 차수) 사용해서 다항식의 계수 
### 2. poly1d() :직선을 그리기 위한 함수 반환
### 3. linspace() : 직선에 필요한 x값 생성

```ts
# '인구수'와 CCTV '소계' 회귀식 계수 
polyfit_result = np.polyfit(data_result['인구수'], data_result['소계'], 1)
func = np.poly1d(polyfit_result)
x_nums = np.linspace(100000, 700000, 100)
```
#### 기울기 : 4.51851962e-03
#### 절편 : 1.90623665e+03

### 그래프 그리기
- 분산형(산점도) 그래프
- 회귀선 표시
```ts
plt.figure(figsize=(6,4))
plt.scatter(data_result['인구수'], data_result['소계'], s=50)

plt.plot(x_nums, func(x_nums), ls='dashed', lw=3, color='g')
plt.xlabel('인구수')
plt.ylabel('CCTV 대수')
plt.grid()
plt.show()
```

## 설득력 있는 데이터 만들기
### 오차 계산하고 큰 순으로 데이터 정렬
- abs() :오차를 양수로 
- 오차 계산해서 '오차' 열 추가

```ts
polyfit_result = np.polyfit(data_result['인구수'], data_result['소계'], 1)
func = np.poly1d(polyfit_result)
x_nums = np.linspace(100000, 700000, 100)
data_result['오차'] = np.abs(data_result['소계'] - func(data_result['인구수']))
data_result_sort = data_result.sort_values(by='오차', ascending=False)
```

## 오차 반영한 그래프
- 오차 수치 색상표현
- 오차 큰 10개 : 텍스트 출력
- text(x위치, y위치, 출력 텍스트, 글자 크기)
- x * 1.02 : 점보다 약가 오른쪽 y * 0.98 : 약간 아래로
- data_result_sort.index[n] : 오차가 큰 10개 구 선택
```ts
plt.figure(figsize=(10,6))
plt.scatter(data_result['인구수'], 
            data_result['소계'],
            c=data_result['오차'],
            s=50)

plt.plot(x_nums, func(x_nums), ls='dashed', lw=3, color='g')


for n in range(10):
    plt.text(data_result_sort['인구수'][n] * 1.02,
            data_result_sort['소계'][n] * 0.98,
            data_result_sort.index[n],
            fontsize=7)

plt.xlabel('인구수')
plt.ylabel('CCTV 대수')
plt.grid()
plt.colorbar()
plt.show()
```
