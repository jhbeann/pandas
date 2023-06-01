## 쇼핑몰 크롤링
---
- 상품의 시세 파악을 위해 쇼핑몰 데이터 추출.파악
- 특정 사이트 선정. 상품의 가격변화 확인
- 구성이 변하지 않는 사이트.
```ts
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity="all"

from urllib.request import urlopen
from bs4 import BeautifulSoup
import requests
import pandas as pd
```
```ts
# 사이트 접속 / html 소스 추출
url ='https://jolse.com/category/toners-mists/1019/?page=1'
htmls = urlopen(url)
htmls = htmls.read()

bs_obj = BeautifulSoup(htmls, 'html.parser')
```

[참고]
- 요청시 ssl에러 발생하면 보안연결 시도
```ts
# import ssl
# context = ssl._create_unverified_context()
# url ='http://jolse.com/category/toners-mists/1019/'
# html = urlopen(url, context=context)
# htmls=html.read()
# bs_obj = BeautifulSoup(htmls, 'html.parser')
# bs_obj
```

### 화장품 쇼핑몰
- 제품명/가격/세일가격
- 페이징 기능을 사용하는 쇼핑몰이므로 페이지를 다르게 접근해서 모든 제품 크롤링(토너/미스트)
- 페이지 구조 : 제품 목록이 2그룹 (추천 상품 / 일반 상품)
    - 추천 상품 2개는 모든 페이지에 동일

```ts
uls = bs_obj.findAll('ul',{'class':'prdList grid5'})
# 추천 상품 : 목록 2개 + 2*2 정상가격/세일가격)
boxes = uls[0].findAll('li')
# 일반 상품 : 목록 20 + 20*2 (정상가격/세일가격)
boxes = uls[1].findAll('li')
```
### 전체 상품 정보 추출
- 상품 정보만 담고 있는 div 태그
```ts
boxes = bs_obj.findAll('div',{'class':'description'})
```
#### 1) 상품명 추출
```ts
for box in boxes :
    strong_tag = box.find('strong',{'class','name'})
    print(strong_tag.text.split(':')[1])
# 구분자 : 로 분리 / (인덱스로 두번째)

# 상품명을 리스트로 생성

product_list = []

for box in boxes :
    strong_tag = box.find('strong', {'class': 'name'})
    product_list.append(strong_tag.text.split(':')[1])
```

### 2) 상품 가격 추출
- 상품 가격은 따로 선택자로 지정되어 있지 않기 때문에
- description boxes 에서 찾기 
- 먼저 ul 태그 확인
    - 5개의 span
    - 두 번째[1] span : 정상가
    - 마지막[-1] span : 할인가 
```ts
boxes[0].find('ul')
# 정상가격
boxes[0].find('ul').findAll('span')[1].text
# 할인가격
boxes[0].find('ul').findAll('span')[-1].text
```

```ts
for box in boxes:
    strong_tags = box.find('strong', {'class':'name'})    
    print('상품명 :', strong_tags.text.split(':')[1])
    price = box.find('ul').find_all('span')[1].text
    print('정상가 :', price)
    sale_price = box.find('ul').find_all('span')[-1].text
    print('할인가 :', sale_price)
```
### 수집 정보 df로 저장
- 첫 페이지 모든 상품에 대해서 
- 접속 / 파싱  
- 수집  
- df로 저장

```ts
url ='https://jolse.com/category/toners-mists/1019/?page=1'
htmls = urlopen(url)
htmls = htmls.read()
bs_obj = BeautifulSoup(htmls, 'html.parser')

prd_list = []
price_list = []
sale_price_list = []

# 전체 상품 데이터 추출
boxes = bs_obj.findAll('div',{'class':'description'})

# 전체 제품 정보 출력
for box in boxes:
    strong_tags = box.find('strong', {'class':'name'})  
    prd_list.append(strong_tags.text.split(':')[1])  # 상품명 리스트
    price = box.find('ul').find_all('span')[1].text  
    price_list.append(price) # 정상가격 리스트
    sale_price = box.find('ul').find_all('span')[-1].text
    sale_price_list.append(sale_price) # 세일 가격 리스트
# df 생성
product_df = pd.DataFrame({
    '품목' : prd_list,
    '가격' : price_list,
    '세일가격' : sale_price_list
}, index = range(1,len(prd_list)+1))
```
## 여러 페이지 크롤링

- 현재 쇼핑몰은  여러 페이지 구성
    - 현재 페이지 뿐 아니라 다른 모든 페이지에도 반복 적용할 수 있도록 함수로 작성
    
---
- 함수명 및 수행 기능  
    - (1) get_request_product(url) : 접속 및 파싱  
            - url 받아서 접속 및 파싱   
            - 반환값 : bs4 객체  
    - (2) get_proudct_info(box) : 상품 정보 추출  
            - box 정보 받아서 상품 정보 추출 후 반환  
            - 반환값 : 상품 1개의 상품명/정상가격/세일가격을 추출해서   
                       - 딕셔너리로 반환  
    - (3) get_page_product(url) : 각 페이지에서 상품 추출하고 df에 추가  
           - 전달 받은 url 페이지에서 box 추출하여      
           - get_proudct_info(box)에 전달하고 반환된 1개의 상품 정보로    
           - 데이터프레임 생성 및 행 추가   

```ts
# 최종 상품 정보 저장할 빈 데이터프레임 생성
prd_dic = {'품목' : [],
           '정상가격' : [],
           '세일가격' : []}

product_df_final = pd.DataFrame(prd_dic)
product_df_final
```
1) 접속 및 파싱 기능 수행하는 함수
```ts
def get_request_product(url):
    try:
        htmls= urlopen(url)
        htmls= htmls.read()
        bs_obj = BeautifulSoup(htmls,'html.parser')
    except :
        print("접속 및 파싱 오류")
    return bs_obj
```

2) 1개 상품 정보 추출
- box 정보 받고 상품 정보 추출 후 반환
- 반환값 : 상품명/정상가격/세일가격을 추출해서 딕셔너리로 반환
- 각 페이지에서 함수 호출해 상품 정보 받기

```ts
def get_product_info(box):
    try:
        strong_tags = box.find('strong',{'class':'name'})
        prd_name = strong_tags.text.split(':')[1] # 상품명 
        price = box.find('ul').find_all('span')[1].text # 정상가격
        sale_price = box.find('ul').find_all('span')[-1].text # 세일가격
    except :
        print("상품정보추출오류")
    return {'품목':prd_name,'정상가격':price,'세일가격':sale_price} 
```
3) url 페이지에서 상품 추출 / df에 추가 

```ts
def get_page_product(url) :
    global product_df_final
    print(url)
    try :
        # 접속 및 파싱 함수 호출하면서 url 전달 후 bs4 객체 받음
        bs_obj = get_request_product(url)
        
        # 페이지 내 전체 상품 추출
        boxes  = bs_obj.findAll('div',{'class':'description'})

        # 첫 페이지 : 추천 상품 2개 포함
        # 두번째 부터 2개 추천상품 제외하고 상품 정보 추출
        # 두 번째 이후 페이지 번호 확인 : https:// ..... /?page=2
        # url을 = 구분자로 split() 해서 오른쪽 값([1]: 두 번째)이 페이지 번호

        if url.split('=')[1] != '1' : # 페이지 번호가 1이 아니라면
            boxes = boxes[2:] # 세 번째 상품부터 끝까지 추출
    except :
        print("페이징 처리 오류")
        
    # 각 상품마다 정보 추출하고 df 생성, 기존 df에 행으로 추가
    for box in boxes :
        res = pd.DataFrame(get_proudct_info(box), index=range(1, 2)) # 형식적 인덱스 추가
        product_df_final = pd.concat([product_df_final, res], axis=0, ignore_index=True)
```


### 스칼라 값으로 데이터프레임 생성 시 오류 발생
- 해결 방법
    1) 리스트로 변환
    2) 데이터프레임 생성 시 index 설정


### 여러 페이지 추출
- 쇼핑몰 url 분석
- 각 페이지에 대하여 url에 파라미터가 다르게 전송됨
- https://jolse.com/category/toners-mists/1019?page= + 페이지번호
- url 작성시 페이지 번호를 따로 부착

---

### 페이지 구조: url + 페이지 번호
#### 기본 페이지 설정 & 기본 페이지 + 번호 =>새로운 url 생성
``` ts
base_url = 'https://jolse.com/category/toners-mists/1019?page='

# 페이지 추출 예
# for i in range(1, 6) : # 1~5 페이지까지 추출
#     url = base_url + str(i)
#     print(url)
#     get_page_product(url)
```

### 마지막 페이지 번호 추출
```ts
url ='https://jolse.com/category/toners-mists/1019/?page=1'
htmls = urlopen(url)
htmls = htmls.read()
bs_obj = BeautifulSoup(htmls, 'html.parser')

last_page = bs_obj.find('a', {'class':'last'})['href'].split('=')[1]
last_page
```
```ts
base_url = 'https://jolse.com/category/toners-mists/1019?page='

# 1~마지막 페이지(26)까지 추출
for i in range(1, int(last_page)+1) : 
    url = base_url + str(i)    
    get_page_product(url)
```

```ts
product_df_final.to_csv('../crawl_data/shopping.csv', index=0)
```
