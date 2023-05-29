# Matplotlib

## 유형


## 명령어 설치
```ts
import matplotlib.pyplot as plt
%matplotlib inline
# 매직 명령 : 주피터 노트북 사용 시
# 노트북 내부에 그림을 표시하도록 지정하는 명령어
```

1) plot() : 선 (변화 보여줌)
-  show() : 보여주기

## 그래프 관련 함수
figure(x,y) : 그래프 크기 설정 : 단위 인치    
title() : 제목 출력     
xlim(범위값): x 축 범위            
ylim(범위값) : y 축 범위            
xticks():yticks() : 축과 값을 잇는 실선               
legend() : 범례                 
xlabel() : x축라벨(값)                  
ylabel() : y축라벨(값)                  
grid() : 그래프 배경으로 grid 사용 결정 함수                        

-----------
## 기본 문법
선  : plt.plot([]) // [] 안에 값 입력 (y축) & x축은 자동 생성                
그래프 보여주기 : show()

축 표시 값 : xticks(), yticks()
plt.figure(figsize=(, )) # 가로,세로


|linestyle|  |
|:--:|:--:|
|solid||
|dashed||
|dotted||
|dashdot||

```ts
plot.plot(x,y ,color = 'green', linestyle='dashed',marker='o')
plt.show()
```

## 스타일 약자
- color(c) : 선색상
-  linewidth(lw) : 선굵기
- linestyle(ls) : 선스타일
- marker : 마커의 종류
- markersize(ms) : 마커의 크기
- markeredgecolor(mec) : 마커 테두리 색삭
- markeredgewidth(mew) : 마커 테두리 굵기
- markerfacecolor(mfc) : 마커 색상
- r : red
- g : green
- b : blue
```ts
plt.plot([10,20,30,40], [1,4,9,16],
        c='b', lw=5, ls='--', marker='o', ms=15, mec='g', mew=5, mfc='r')
plt.xlim(0, 50) # x 축 범위
plt.ylim(-10, 30) # y 축 범위
plt.title('마커 스타일 적용')
```

## 크기 설정
```ts
plt.figure(figsize(5,3))
```
## 눈금 레이블 지정
```ts
# 튜플
plt.xticks(x, ("10대", '20대', '30대', '40대', '50대', '60대'))
# 리스트
plt.xticks(x, ["10대", '20대', '30대', '40대', '50대', '60대'])

# 리스트 내 for 사용
plt.xticks(x, [f'{i}대' for i in range(10,70,10)])
plt.xticks(x, [str(i) + '대' for i in x])
```

### 그래프 제목
plt.title
### 그리드 표시
plt.grid(True)
### 그리드 삭제
- grid(False) | grid(Ture) 라인 삭제

## 화면 분할/ 여러 그래프
- subplot(인수1,인수2,인수3)  , 생략 가능

- subplot(221) # 2행 2열 1번 그래프
- 그리드 형태 Axes 객체 
- tight_layout(pad=) : 플롯간 간격


## plt.subplots(행,열)
- 여러개의 Axes 객체 동시 생성
- 행렬 형태로 반환
```ts
fig, axes = plt.subplots(2, 2) # 2행 2열
np.random.seed(0)

axes[0, 0].plot(np.random.rand(5))
axes[0, 0].set_title("axes1")

axes[0, 1].plot(np.random.rand(5))
axes[0, 1].set_title("axes2")

axes[1, 0].plot(np.random.rand(5))
axes[1, 0].set_title("axes3")

axes[1, 1].plot(np.random.rand(5))
axes[1, 1].set_title("axes4")

# 플롯 간격 자동 맞춤
plt.tight_layout()
plt.show()
```


## sin/cos 함수

```ts
plt.plot(t,np.sin(t))
plt.plot(t,np.cos(t),color='red')
plt.title('sin /cos 곡선')
plt.xlabel('time')
plt.ylabel('Amplitude')
plt.grid(True)
plt.show()
```

## 범례표시
- plt.legend(loc=, ncol= )  
- loc = 0/1/2/3/4/5/6/7/8/9/10 # 범례표시 위치값
- loc = (x,y)
- ncol= 열의 개수

- 범례를 표시하기 위해서는 plot에 label 속성이 추가되어 있어야 함
    - plt.plot(x,y,label='a')


## 세로 막대 그래프 : bar()
```ts
plt.bar(x,y)
plt.xticks(x,xlabel)
plt.yticks(y)
plt.xlabel('가나다라')
plt.ylabel('빈도수')

```