# 응용예제1_서대문구_치킨집_분포

#### 분석 목표
- 서대문구에서 치킨집이 가장 많은 동 확인  

분석 내용 및 순서  
- (1) [위생업태명]에서 통닭 또는 치킨이 포함되어 있고  
- (2) [영업상태명]이  '영업/정상'인 데이터 중에서  
- (3) 주소에 '서대문구'가 포함된 데이터만 추출하여  
- (4) 동 데이터만 추출한 후  
- (5) 동별 치킨집 개수 구해서  
- (6) 트리맵으로 시각화  

### 자료 출처
- 데이터 다운로드 : LOCALDATA 웹 사이트 
- http://www.localdata.kr/

## 데이터 읽어오기
### 인코딩 오류 해결 --> encoding='cp949'
```ts
data = pd.read_csv('../data/서울특별시_일반음식점.csv', encoding='cp949', low_memory=False)
```

## (1) [위생업태명] 통닭 or 치킨 포함 
- 위생업태명 열이름 확인  
- 문자열.contains()
    - NaN : 에러 ==> na=False로 설정
```ts
data.위생업태명.str.contains('통닭|치킨', na=False)

# set() 함수 : 중복 제거 필터링
set(data.위생업태명.values)
len(set(data.위생업태명.values))

# 위 두 내용 합치기
# 필요 추출 : 통닭 또는 치킨이 포함된 열의 값 추출
set(data.위생업태명[data.위생업태명.str.contains('통닭|치킨', na=False)])

```

## (2) [영업상태명]이  '영업/정상'인 데이터 

### 조건 인덱싱 : 추출 조건으로 일치하는 데이터 확인
### isin() 사용 : 시리즈.isin([데이터 리스트])
```ts
# 조건
(data.영업상태명 == '영업/정상') & data.위생업태명.isin(['호프/통닭', '통닭(치킨)'])

# 추출
data_final = data[(data.영업상태명 == '영업/정상') & data.위생업태명.isin(['호프/통닭', '통닭(치킨)'])]
```

## (3) 주소에 '서대문구'가 포함된 데이터만 추출
- 필요한 컬럼(소재지전체주소,위생업태명)
- 소재지전체주소.str.contains() 사용
```ts
data_final_address = data_final[['소재지전체주소', '위생업태명']]
result = data_final_address.소재지전체주소.str.contains('서대문구', na=False)
# 서대문구 치킨집 주소 df
data_seo_address = data_final_address[result]
```

## (4) 동 데이터만 추출
- 동이름 위치 : 인덱스 11 ~ 16번째 글자 
- 일부 글자 추출 : str.slice() 함수 사용
- 동 뒤 나온 숫자 제거 
- 정규식 표현 : regex=True => 경고 없음
- 동 뒤 공백 제거 
```ts
dong = data_seo_address.소재지전체주소.str.slice(start=11, stop=17) # start ~ end-1

dong = dong.str.replace('[0-9]', '', regex=True)
dong = dong.str.replace(' ', '')
```
## (5) 동별 치킨집 개수 구하기
- 동이름 확인 (중복 제거)
- 동 뒤의  - 제거
- 잘못된 동이름 변경 : 옥천동번 --> 옥천동
- 동별 치킨집 개수 확인 value_counts()
- 내림차순 정렬
```ts
dong = dong.str.replace('-', '')
dong = dong.str.replace('옥천동번', '옥천동')
set(dong.values)
dong_chicken_count = dong.value_counts()

dong_chicken_count.sort_values(ascending=False, inplace=True)
```

## (6) 트리맵으로 시각화 
```ts
!pip install squarify
import squarify
squarify.plot(dong_chicken_count, label= dong_chicken_count.index)
```
