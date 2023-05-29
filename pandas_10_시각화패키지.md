#  시각화 도구 : Seaborn

## 1) plot()

## seaborn 스타일링 함수
1. set_style() : 전반적인 모양
    - 테마 : darkgrid, whitegrid, dark, white, ticks
1. despine() : 축선 여부
    - right=T/F,left=T/F,top=T/F,bottom=T/F
    - T : 표시 안함/ F : 표시 
1. set_context() :보고서,ppt 활용 스타일링
    -  paper, notebook, talk, poster(기본값 : notebook)
    - 폰트 크기 : sns.set_context() 함수의 font_scale 
```ts
np.linspace(1,14,100) # 1~14 범위에서 일정 간격으로 100개 생성
x = np.linspace(0, 14, 100)
y1 = np.sin(x)
y2 = 2*np.sin(x+0.5)
y3 = 3*np.sin(x+1.0)
y4 = 4*np.sin(x+1.5)

sns.set_style('dark')
sns.set_context('notebook', font_scale=0.5)
plt.figure(figsize=(6,4))
plt.plot(x,y1, x,y2, x,y3, x,y4)
sns.despine(left=True, bottom=True)
plt.show()
```
## tips 데이터셋
```ts
# 데이터 로드
tips = sns.load_dataset('tips')
```

###  Distribution Plot
- Distribution Plot은 데이터의 분포를 시각화하는데 도움이 되는 그래프
     1. Hist Plot : histplot()  
     2. KDE Plot : kdeplot()  
     3. Box Plot : boxplot()  
     4. Swarm Plot : swarmplot()

### 1. Hist Plot
- 히스토그램
- 하나 또는 두 개의 변수 분포를 나타내는 전형적인 시각화 도구로 
- 범위에 포함되는 관측수를 세어 표시
```ts
 # 카테고리형 데이터 : 요일별
sns.histplot(x=tips['day'])
```
### 2. KDE Plot
- 하나 또는 두 개의 변수에 대한 그래프
    - histplot은 절대량 && kdeplot은 밀도 추정치 시각화
    - 연속된 곡선의 그래프
```ts
sns.kdeplot(x=tips['total_bill'])
```
- 주의! = 카테고리값을 tips 로 사용시 에러

## 3. Box Plot
```ts
# x 값만 지정
sns.boxplot(x=tips['total_bill']) 
# y 값만 지정
sns.boxplot(y=tips['total_bill'])

sns.boxplot(x=tips['day'], y=tips['total_bill'])
sns.boxplot(x='day', y='total_bill', data=tips)

# palette 설정 : palette = '파레트명' Set1, Set2, Set3
sns.boxplot(x='day', y='total_bill', data=tips, palette='Set3')
```

## 범주형 변수 처리
- ## **hue** 인수에 지정
- 카테고리 값에 따라 시각화
- 범주형 변수 : smoker(yes/no)
- 범례는 자동 빈 공간에 배치


```ts
sns.boxplot(x='day', y='total_bill', data=tips, 
            hue='smoker',  palette='Set3')
```

## 4. Swarm Plot
- 데이터 포인트 수 -> 분포 표시
- 데이터가 모여 있는 정도 확인
```ts
plt.figure(figsize=(6, 4))
# boxplot과 swarmplot과 같이 사용 가능
sns.boxplot(x='day', y='total_bill', data=tips)
sns.swarmplot(x='day', y='total_bill', data=tips, palette='husl')
plt.show()
```

## lmplot() : 회귀선 기본 출력
- height : 그래프 크기
- hue : 카테고리 그룹화 / 한 그래프에 모두 표시
- col : 카테고리 그룹화 / 그룹 개수만큼 그래프 (따로표시)
    - fit_reg=False : 회귀선 생략 가능
    - ci = None : 신뢰구간 없음 (95:95% 신뢰구간)
    - scatter_kws = {'s':50, 'alpha':1} : 점의 크기 및 투명도

## lmplot() : 산점도와 함께 선형회 귀선 표시
### 선 주변 영역 : 신뢰 구간의 영역
```ts
sns.set_style('darkgrid')
sns.lmplot(x='total_bill', y='tip', data=tips, height=5,ci=95)
plt.show()
# 회귀선에 있는 그림자는 신뢰구간(95%)
```
## 참고 : regplot() 동일
-  그러나 여러 옵션 다름 : size, height, hue 사용하면 오류
```ts
# 카테고리 변수 사용
sns.set_style('darkgrid')
sns.lmplot(x='total_bill', y='tip', hue='smoker', data=tips, 
            palette='Set1',  height=5, scatter_kws={'s':30, 'alpha':0.7})
plt.show()
# hue 대신 col='smoker' 사용시 따로 그래프 표시
# => 카테고리 값이 많은 경우 col_wrap=2 : 2행 배치
```


## heatmap
- 열 분포도
- 2차원 수치데이터 색으로 표시
- 대용량 데이터 시각화
- 두 개 카테고리 값 변화
```ts
# heamap()에 사용할 데이터셋 : 항공기 승객수 데이터셋 사용
flights = sns.load_dataset('flights')
# 연도별, 월별 항공기 승객수 출력
# pivot_table() 사용해서 변환
flights_pv = pd.pivot_table(flights,
                           index='month',
                            columns='year',
                            values='passengers',
                            fill_value=0)
# heatmap() 함수 사용해서 그래프 그리기
# annot=True : 각 셀에 숫자 출력 
# fmt='f'  : 포맷 (실수)
# fmt='d'  : 포맷 (정수)

plt.figure(figsize=(6,4))
sns.heatmap(flights_pv,annot=True, fmt='d',cmap='RdBu')
```

## 상관 행렬을 통한 heatmap() : 상관관계 시각화
- df.corr() 사용 : 상관행렬
```ts
# 상관계수 구하기 : 상관행렬 생성
tips.corr()
# 히트맵으로 표현 : 실수 fmt='f'   (fmt='d' 하면 에러)
plt.figure(figsize=(6,4))
sns.heatmap(tips.corr(), annot=True, fmt='f', cmap='viridis')
plt.show()
```

## pairplot()
- 3차원 이상의 데이터에 적용
- grid 형태
- 열 조합에 대한 scatterplot()
- 같은 데이터 지점(대각선) : 히스토그램

## iris(붓꽃) 데이터 사용
### 꽃잎 (petal), 꽃받침(sepal)의 너비와 폭으로 종 구분
```ts
iris = sns.load_dataset('iris')
sns.set(style='ticks')
sns.pairplot(iris)

sns.pairplot(iris, hue='species', diag_kind='hist')
plt.show()
```

# folium 패키지
- 지도 이용한 시각화 도구
- 설치 :  !pip install folium
- import folium


## Open Street Map 기반 지도
### 맨해튼 : 위도: 40.6643, 경도: -73.9385
```ts
map_osm = folium.Map(location=[40.6643, -73.9385], zoom_start=13)
```
## 지도 스타일 : tiles 인수 (Stamen Terrain, Stamen Toner 두개)

## 마커 및 popup 설정
1. 마커 설정
    - folium.Marker([위도,경도],popup=,icon=) 
    - CircleMarker([위도,경도],popup=,radius=,color=,fill_color=,).add_to(seoul_city_map)
    - html 파일로 저장 
    ```ts
    seoul_cityhall_map = folium.Map(location=[37.566345, 126.977893], zoom_start=17)
    folium.Marker([37.566345, 126.977893], popup='시청').add_to(seoul_cityhall_map)
    folium.CircleMarker([37.5658049, 126.9751461000007],
                   popup='덕수궁',
                   radius=50,
                   color='red',
                   fill_color='blue').add_to(seoul_cityhall_map)
    seoul_cityhall_map.save('../data/seoul_cityhall_map.html')
    seoul_cityhall_map
    ```

## 단계 구분도
    - 데이터를 지도 그림에 반영
    - 보통 folium 에 layer로 올려서 표시
    - 사용 인수
        - geo_data : 지도 파일 경로와 파일명
        - data : 지도에 표현되어야 할 값 변수
        - columns =  [key로 사용할 data, 실제 data의 필드명]
        - key_on : 지도 경계파일인 json에서 사용할 키 값이며 지도 Data는 표준 형식으로 만들어 져야 함
        - key_on 지칭 문법 : feature(키워드).json에서 나타나키 필드명
    - folium.LayerControl().add_to(map)  : 단계구분도를 map에 표시하는 함수

```ts
geo_path = '../data/skorea_municipalities_geo_simple.json'
geo_str = json.load(open(geo_path, encoding='utf-8'))

map = folium.Map(location=[37.5502, 126.982],zoom_start =11,tiles='Stamen Toner')
map
```

```ts
# 단계 구분도에서 사용할 data : population.csv 
# 구별 CCTV 인구수
pop_df = pd.read_csv('../data/population.csv', index_col='구별')
# 단계 구분도 :  choropleth()
map.choropleth(geo_data=geo_str,
              data=pop_df,
              columns=[pop_df.index, '소계'],
              key_on = 'feature.properties.name',
              fill_color='PuRd',
              legend_name='인구 현황')

folium.LayerControl().add_to(map)
map

