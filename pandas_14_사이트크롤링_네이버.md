## 사이트 크롤링
1. 사이트 소스내에서 특정 문자열(data)을 지칭하는 선택자 얻기
    - 크롬 개발자 도구의 기능 사용
    - 검사할 페이지의 요소 선택
2. 전체 코드에서 수집하려고 하는 데이터의 위치를 찾고
    - 태그 파싱 후 필요 데이터 추출

### 1) 사이트 크롤링 
- 기본 메뉴 문구 추출
```ts
url = 'https://www.tistory.com/'
```
- 요청 후 응답받기
```ts
html = urlopen(url)
```
- 파서 객체 생성
```ts
bs_obj = bs4.BeautifulSoup(html, 'html.parser')
```
- 기본 메뉴 박스 : id : kakaoGnb 인 <div> 추출
```ts
tistory_menu = bs_obj.find('div', {'id':'kakaoGnb'})

ul = bs_obj.find('ul', {'class':'list_gnb'})

a_tag = bs_obj.findAll('a', {'class':'link_gnb'})

# 메뉴 항목 추출 : <a> 와 텍스트 모두 
li_list = tistory_menu.select('li')
for li in li_list:
    print(li.select('a'),li.text)
```

#### 연습문제 : 오른쪽 하단의 문의 목록 모든 항목 추출 : 텍스트만 추출

```ts
q_list = bs_obj.find_all('span',{'class':'tit_question'})

for i in q_list:
    print(i.text)

# 메뉴가 궁금할 땐
# 사용하다 궁금할 땐
# 정책이 궁금할 땐
# 도움이 필요할 땐

for span in bs_obj.selct('#daumFoot span'):
    print(span.text)
```

