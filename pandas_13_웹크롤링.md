# 웹 크롤링

### 1. 웹 크롤링 
- html 문서 내용 읽어오기
    - url 접속
        - urllib 패키지의 urlopen()
        - urllib : URL 주소 넣고 실행시 텍스트로 변환
    - 문서 전체 읽어오기 : read() 함수
### 2. 파싱
- html 문서에서 원하는 내용 추출
- BeautifulSoup 라이브러리
- find() / findAll() / find_all() 함수
- select() / select_one()

### 1. 웹 크롤링 
- urllib 패키지 사용한 소스 추출

```ts
from urllib.request import urlopen
url = "https://www.tistory.com"
html = urlopen(url)
text = html.read()
# text.decode('utf8') # 한글 깨짐 : 인코딩
```

- BeautifulSoup 패키지의 파싱

```ts
!pip install bs4
import bs4
url = "https://www.tistory.com"
html = urlopen(url)
# bs4 파싱 객체를 반환
bs_obj = bs4.BeautifulSoup(html, 'html.parser')
```

### BeautifulSoup 함수 사용
- find() : 첫 번째 태그 추출   find('태그명')
- find().text : 텍스트 추출
 ```ts
 # test용 html 문서 내용
html_str = "<html><div>hello</div></html>"
# html 코드 -> 파싱객체로 변환
bs_obj = bs4.BeautifulSoup(html_str, 'html.parser')
```

```ts
html_str = """
<html>
    <body>
        <ul>
            <li>hello</li>
            <li>bye</li>
            <li>welcome</li>
        </ul>
    </body>
</html>
"""
```
```ts
bs_obj = bs4.BeautifulSoup(html_str, 'html.parser')
# <ul> 추출
ul = bs_obj.find('ul')
# <ul> 중 첫번째 <li> 반환
ul.find('li')
# 모든 li 태그를 리스트로 반환 :findAll()
ul.findAll('li')
# 특정 원소의 텍스트 출력 ('welcome')
ul.findAll('li')[2].text

# <li> 내 text 추출 : 반복문 이용
lis = ul.findAll('li')
for li in lis:
    print(li.text)

# <ul> 모든 text를 문자열로 묶어서 반환
ul = bs_obj.find('ul')
ul.text
ul.text.split('\n')
```


```ts
html_str = """
<html>
    <body>
        <ul class="greet">
            <li>hello</li>
            <li>bye</li>
            <li>welcome</li>
        </ul>
        <ul class="reply">
            <li>ok</li>
            <li>no</li>
            <li>sure</li>
        </ul>
    </body>
</html>
"""
```
- 태그 중에 특정 속성을 갖고 있는 태그를 추출    ('태그명', {'속성명':'속성값'})

- class 속성값이 greet인 ul 태그 내의 모든 li의 텍스트값 추출
- 메소드 체인 방식 가능 : find().find()
```ts
bs_obj = bs4.BeautifulSoup(html_str, 'html.parser')

bs_obj.find('ul', {'class':'greet'})
# 1방법
lis = bs_obj.find('ul', {'class': 'greet'}).findAll('li')

for li in lis:
    print(li.text)
# 2.방법
uls = bs_obj.findAll('ul', {'class' : 'greet'})
for ul in uls:
    for li in ul.findAll('li'):
        print(li.text)
```

### id 태그 속성의 특징 : id 속성값이 중복되면 안됨

```ts
html_str = '''
<html>
    <body>
        <h1 id='title'>Hello Python</h1>
        <p id="crawling">웹 크롤링</p>
        <p id="parsing">파싱</p>
    </body>
</html>'''
```
```ts
bs_obj = bs4.BeautifulSoup(html_str,'html.parser')
# id가 title인 태그,텍스트 반환
bs_obj.find('h1',{'id':'title'})
bs_obj.find('h1',{'id':'title'}).text

# id가 parsing인 태그,텍스트 반환
bs_obj.find('p',{'id':'parsing'})
bs_obj.find('p',{'id':'parsing'}).text
```

### bs4 형제 노드 찾기
- next_sibling
```ts
html_str = """
<html>
    <body>
        <h1>파이썬 프로그래밍</h1>
        <p>웹 페이지 분석</p>
        <p>크롤링</p><p>파싱</p>        
    </body>
</html>
"""
```
```ts
bs_obj = bs4.BeautifulSoup(html_str, 'html.parser')
# 문서에서 첫번째 p태그
bs_obj.find('p')
# 문서에서 나타나는 모든 p태그
bs_obj.findAll('p')
# 인덱스 1 (두 번째) p태그
bs_obj.findAll('p')[1]
```
```ts
p1 = bs_obj.find('p')

p1.next_sibling
# 줄바꿈한 경우 형제 태그로 '\n'을 인식
p1.next_sibling.next_sibling #<p>크롤링</p> 그 다음 p태그
p1.next_sibling.next_sibling.next_sibling # <p>파싱</p>
```

### 태그내 특정 값 추출
- a 태그의 href 속성
```ts
html_str = """
<html>
    <body>
        <ul class="ko">
            <li><a href="https://www.naver.com/">네이버</a></li>
            <li><a href="https://www.daum.net/">다음</a></li>
        </ul>
        <ul class="sns">
            <li><a href="https://www.goole.com/">구글</a></li>
            <li><a href="https://www.facebook.net/">페이스북</a></li>
        </ul>
    </body>
</html>
"""
```
```ts
bs_obj = bs4.BeautifulSoup(html_str, 'html.parser')

# 첫 번째 a 태그
bs_obj.findAll('a')[0]
# 태그객체의 특정 속성 지칭
# 태그객체['속성명']
# 첫 번째 a 태그의 href 속성
bs_obj.findAll('a')[0]['href']

# 속성명이 class 인 태그 추출
bs_obj.find('ul')['class']

# 모든 링크 추출
a_list = bs_obj.findAll('a')
a_list

for a in a_list:
    print(a['href'])

# https://www.naver.com/
# https://www.daum.net/
# https://www.goole.com/
# https://www.facebook.net/

```

```ts
import bs4
html_str = """
<html>
   <body>
    	<div id="wrap">
        	<div id="mainMenuBox">                	
                <ul>  
                    <li><a href="#" id='fashion'>패션잡화</a></li>    
                    <li><a href="#">주방용품</a></li>                     	          
                    <li><a href="#">생활건강</a></li>
                    <li><a href="#">DIY가구</a></li>
                </ul>
            </div>
        	<div>
            	<table>
                	<tr><td><img src="shoes1.jpg"></td>
                    	  <td><img src="shoes2.jpg"></td>
                    	  <td><img src="shoes3.jpg"></td></tr>
                    <tr id="prdName"><td>솔로이스트<br>걸리쉬 리본단화</td>
                    	  <td>맥컬린<br>그레이가보시스트랩 펌프스</td>
                          <td>맥컬린<br>섹슈얼인사이드펌프스</td></tr>
                    <tr id="price"><td>100,000원</td><td>200,000원</td><td>120,000원</td></tr>
                </table>
            </div>
            <div id="out_box">
            	<div class="box">
                	<h4>공지사항</h4>
                    <hr>
                    <a href="#">[배송] : 무표배송 변경 안내 18.10.20</a><br>
                    <a href="#">[전시] : DIY 가구 전시 안내 18.10.31</a><br>
                    <a href="#">[판매] : 11월 특가 상품 안내 18.11.05</a><br>
                    <div>공지사항 확인 필수</div>
                </div>
                <div class="box">
                	<h4>커뮤니티</h4>
                    <hr>
                    <a href="#">[레시피] : 살 안찌는 야식 만들기</a><br>
                    <a href="#">[가구] : 헌집 새집 베스트 가구</a><br>
                    <a href="#">[후기] : 배송이 잘못 됐어요 ㅠㅠ</a><br>
                    <div>커뮤니티 확인 요청</div>
                 </div>
            </div>            
        </div>
    </body>
</html>"""
```

### select() 
- select_one() : 1개 태그 추출
- select() : 여러개 추출
- CSS 선택자 사용 : 아이디 선택자 / 클래스 선택자 / 태그 선택자
- #id: id선택자 : 유일한 태그 선택시
- 띄어쓰기 선택 : 자손 선택
    - div li: div 내 모든 자손 li
- '>' 선택 : 자식선택
    - div > li : div태그 내 자식 태그 li


```ts
bs_obj = bs4.BeautifulSoup(html_str, 'html.parser')

# 모든 <a> 태그 추출
a_list = bs_obj.select('a')
# <a> 태그 텍스트 추출
for a in a_list:
    print(a.text)
# id가 mainMenuBox인 <div> 태그 추출
bs_obj.select('#mainMenuBox')
# find() 사용 시 : {'id':'mainMenuBox'}
# id가 mainMenuBox인 <div> 태그 내 모든 <li> 태그 추출 : 띄어쓰기
bs_obj.select('#mainMenuBox li')

# 클래스가 box인 태그 내 모든 <a> 추출
bs_obj.select('.box a')

# id가 fashion 태그의 텍스트 추출
bs_obj.select('#fashion')[0].text
bs_obj.select_one('#fashion').text

# select() : 리스트로 반환 (값이 1개라도 리스트로 반환)
# 1개인 경우는 select_one()사용

# '공지사항 확인 필수' 텍스트 추출
bs_obj.select('.box div')[0]  # 태그 추출
bs_obj.select('.box div')[0].text
bs_obj.select_one('.box div').text
```