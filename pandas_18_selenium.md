# selenium을 이용한 동적 크롤링
## 동적 크롤링

```ts
import pandas as pd
import numpy as np
from urllib.request import urlopen # 서버 요청/응답 패키지
import bs4 
from bs4 import BeautifulSoup
# 경고 메시지 무시 설정
import warnings
warnings.filterwarnings(action='ignore')
```
### 동적으로 변환된 값 추출
- 셀레니움 사용
* 좋아요 수 추출
```ts
url = 'https://n.news.naver.com/mnews/article/029/0002803319?sid=101'
html = urlopen(url)
bs_obj = bs4.BeautifulSoup(html, 'html.parser')

like_num = bs_obj.find('span',{'class':'u_likeit_text_count num'})
like_num 
# 추출 못함 ---> 동적 데이터 읽어오는 방법 수행
```

### selenium 패키지 모듈 이용한 자동 크롤링
- selenium
    - webdriver라는 API를 통해 운영체제에 설치된 웹 브라우저를 제어하는 함수를 포함한 패키지
    - 써드파티라이브러리이기 때문에 설치 해 줘야 함
    - prompt 에서 설치
        - pip install selenium

### selenium 사용해서 데이터 추출
```ts
# 설치
!pip install selenium
!pip install webdriver-manager

import selenium
from selenium import webdriver
from selenium.webdriver.common.by import By # 셀레니움 4.0부터 포함된 객체(모듈)
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
```

### Selenium 사용해서 데이터 추출 순서
- (1) webdriver 객체 생성  
    - driver = webdriver.Chrome('../driver/chromedirver') # 이전 방법
    - 최신 방법
        - chrome_options = webdriver.ChromeOptions()  
        - driver = webdriver.Chrome(service=Service(....))  
- (2) 페이지 접속 : driver.get(url)  
    - driver.get("https://news.naver.com/main/main.naver?mode=LSD&mid=shm&sid1=101")    
- (3) 페이지 소스 가져오기  
    - html=driver.page_source    
- (4) driver 메소드를 통한 데이터 추출     
    - result = driver.find_element(By.CLASS_NAME, "information")


## 네이버 뉴스에서 좋아요 수 추출
1) webdriver 객체 생성 
2) 페이지 접속 
```ts
chrome_options = webdriver.ChromeOptions()
driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()), options=chrome_options)

driver.get("https://n.news.naver.com/mnews/article/029/0002803319?sid=101")
```
3) driver 메소드를 통한 데이터 추출
- 좋아요 수 추출
- 클래스 선택자로 추출 : <span class="u_likeit_text _count num">
    - CSS 선택자 종류
    - 클래스 선택자 : .클래스명 (앞에 점)
    - 아이디 선택자 : #id
    - 태그 선택자 : 태그명

```ts
# By.CSS_SELECTOR : 선택자 사용 
# 계층 구조(포함구조 선택자도 포함 가능)
element = driver.find_element(By.CSS_SELECTOR, ".u_likeit_text._count.num")
element.text
```

### selenium 사용하여 정적 데이터도 추출 가능
```ts
# 신문사, 제목, 날짜 추출(하위 요소로 선택)
paper = driver.find_element(By.CSS_SELECTOR, ".media_end_head_top_logo img")
paper.get_attribute('title') # 속성 추출

# 또는 직접 선택자 선택
paper = driver.find_element(By.CSS_SELECTOR, ".media_end_head_top_logo_img.light_type")
```


