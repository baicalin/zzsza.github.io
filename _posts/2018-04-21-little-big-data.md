---
layout: post
title: "Little Big Data #1 : 다양한 사람들의 데이터 사이언스 이야기 후기"
subtitle: "Little Big Data #1 : 다양한 사람들의 데이터 사이언스 이야기 후기"
categories: etc
tags: lecture
comments: true
---
Little Big Data #1 : 다양한 사람들의 데이터 사이언스 이야기 [세션](https://festa.io/events/21) 후기입니다!  
(**발표자**가 쓰는 행사 후기^_^...)


<img src="https://www.dropbox.com/s/5l7fz21kmdrutnu/IMG_2424%20%281%29.JPG?raw=1">


## Intro
<img src="https://www.dropbox.com/s/xycjly9kl7bz3gd/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-04-21%20%EC%98%A4%ED%9B%84%2012.14.06.png?raw=1">

- Little Big Data : 다양한 사람들의 다양한 데이터 사이언스 이야기!
- ```Apache Zeppelin```을 만드는 ```Zepl```에서 진행합니다!
- LETS(Local Exchange Trading System)을 통해 네트워킹을 진행합니다!
- [BrewDog](https://www.facebook.com/brewdogkorea/)의 후원을 받았습니다! 

## 한국어 채팅 데이터로 머신러닝 하기
- 조한석님, 스캐터랩 머신러닝 엔지니어
	- 채팅 데이터를 분석해서 썸 혹은 연애를 과학적으로 분석

- 발표에서 다루는 내용
	- 한국어 채팅 데이터를 다룬 경험
	- 상황 문제 해결의 방식	

### 한국어 자연어 처리의 어려움
- Hell 조사
- 언어의 변형이 자유로움

### 커플 채팅 데이터의 어려움
- 혀 꼬인 소리
- 맞춤법 혹은 띄어쓰기
- 채팅에서만 할 수 있는 표현	

### Preprocess
- 형태소 분석기
	- KoNLPY, 꼬꼬마, 트위터, 한나눔 분석기
	- 채팅 데이터를 형태소 분석기에 맞지 않음 ㅠ_ㅠ
	- 형태소 분석기 학습에 사용된 데이터(corpus)의 단어 분포가 채팅 데이터의 단어분포와 다르기 때문에 잘 되지 않음
	- Sejong Corpus는 정말 바르고 깔끔한 데이터
	
- 형태소 분석기 대안
	- 합리적인 기준으로 패턴을 찾고 패턴으로 preprocess
	- 일종의 normalize(표준화)

- tokenizing과 띄어쓰기 교정이 필수!!!
- 띄어쓰기 교정
	- 연속된 글자가 주어졌을 때, 그 다음에 띄어쓰기를 할지 말지를 결정하는 Binary Classification 문제
	- 아이디어 : 오류가 적다고 판단되는 데이터를 선택한 뒤, 전체 데이터에서 조금 등장한 패턴을 자주 등장한 패턴으로 수정
	- 데이터에 존재하지 않는 패턴(=어휘)은 잘못 띄어 쓰게 될 확률이 높음
	- Sequential labeling은 연산량이 많아서 사용하기 힘듬
	- 휴리스틱한 알고리즘을 사용!

	
- Tokenizing 
	- 여러 단어로 이루어진 문장 혹은 어절에서 단어를 구분하는 것
	- 영어의 경우 띄어쓰기 단위로 token이 나뉘지만 한국어는 그렇지 않음
	- token : 의미를 가진 최소 단위
	- 의미를 가진 실질 단위를 단어라고 생각한다면 tokenizing 문제는 문장에서 단어를 추출하는 문제로 생각할 수 있음
	- Cohesion Probability : 연속된 글자의 연관성이 높을수록 단어일 가능성이 높음
	- 김현중님 파이콘 2017 참고하면 좋습니다!

- Word Embedding - Word2Vec
	- 단어 정보를 이용해 단어를 vector로 변환
	- word2vec은 쉽게 사용할 수 있지만 OOV(Out-of-vocaburary) 문제가 있음
	- train시는 문제가 되지 않지만 inference 단계는 문제가 생김

- Fasttext
	- word2vec과 유사하지만 두 단어간의 점수 측정하는 부분이 다름
	- substring 정보를 이용하는 방법(word2vec은 dot product)
	- 글자(character) 단위의 subword를 사용
	- 자모 단위의 subword를 사용
	- 글자 단위보다 자모 단위가 더 작으므로, OOV 문제에 대해 더 유연하게 대처할 수 있음
	- 오타를 잡는 알고리즘을 만들 수 있음

- Sentence Similarity
	- 자연어 처리에서 주로 다루는 문제는 두 document간의 유사도를 비교하는 문제
	- 커플 채팅데이터가 일반 document와 다른 점은 ```short sentence```라는 점  	
	- BOW + Word Embedding
		- word embedding의 성능에 큰 영향
		- 채팅 데이터에서 학습시킨 word embedding 결과가 이상해
		- 유사한 단어 != 뜻이 비슷한 단어 
	- RNN
		- RNN 본질은 language modeling
		- 주어진 sequence에서 다음에 올 단어 혹은 글자를 예측하는 방향으로 embedding

	- Seq2Seq
		- 같은 output이 나오는 방향으로 embedding이 됨 
	- Term vector
		- 애매하게 embedding될 바에 term vector로 표현!
		- 단어의 변화에 민감. 짧은 문장은 문제가 더 생김
		- 단어의 의미 정보를 이용할 수 없음
		
	- ESA Similarity (Explicit Semantic Analysis)
		- 명시적인 정보(=word vector)를 이용
		
- 기타
	- 한국어 자연어 처리는 preprocessing이 80% 이상
	- Zipf's law
		- corpus에서 나타나는 단어들을 사용빈도 순으로 나열하면, 사용빈도와 해당 단어의 수는 반비례
		- 빈도수가 적은 단어는 과감히 쳐내자
	- 문제 정의를 잘 하는 것이 모델을 구현하는 것보다 중요
		- 현실에서 마주치는 문제는 복합적인 요소들이 작용
		- 기술적 뛰어난 모델 적용하는 것도 좋지만, 간단한 통계 혹은 count based 모델을 사용하는 것이 효과적일 수도 있다
	- unlabeled data에 직접 label을 달아보자     
		
## 피자 타임!!
<img src="https://www.dropbox.com/s/4c80wxy8j5343qo/IMG_1230.jpg?raw=1">

<img src="https://www.dropbox.com/s/5nqv46id5nq6eqh/IMG_2479%20%281%29.JPG?raw=1">

- 피자와 BrewDog의 맥주를 즐겼습니다!	
		
## 딥러닝에 필요한 로그 기깔나게 잘 디자인하는 법
- 백정상님, 구글 클라우드 엔지니어

### 지난내용 복습
- 뭘 쌓야아 할지 몰라서 뭐든 쌓아봤어요
- 로그인 액티비티를 로비 입장시에 찍었어요
- 클라이언트 리스펀스를 로그 대용으로 썼어요
- 데이터는 JSON으로 쌓았어요
- 원시 데이터 바로 분석 쿼리를 해요

### 딥러닝이 이걸 해결해줄까?
- 통계적으로 풀지 못하는 문제가 생김
- 비정상적인 데미지를 만드는 플레이어가 핵 유저인지 알고 싶다
- 고전적인 머신러닝 기법으로 풀려고 보니 바운더리가 필요한데, 어떤 데미지가 비정상적인지 정상인지 알 수 없음

### 비정상 데미지를 딥러닝으로 알아보는 방법
- 정상 데이터가 많으면 충분히 학습시키고 비정상 데미지일 가능성 예측
- 많지 않으면 딥러닝 모델에 모든 데미지 데이터를 학습시키고 유저를 클러스터링해서 아웃라이어를 잡자

### 문제를 풀어보자
- 가설 : 플레이어가 게임을 플레이할 때, 같은 환경에서 다른 유저들이 평균 최대 데미지에 비해 많은 데미지를 만드는 유저 그룹이 뭔가 있을 것이다. 그리고 이들은 적을 것이다
- 피쳐 엔지니어링
	- 유저인덱스는 너무 세분화니 제외
	- 스테이지별 max data
	- 캐릭터별 밸런스 격차가 있으므로 캐릭터 넣기 
- 허나 위에껀 상상이었음ㅋ

### 실제 생긴 고민들
- 무슨 모델을 쓸 것인가 선정하지 않았음
	- 클러스터링 모델도 다양
	- 꼭 딥러닝을 써야하는가?
	- 오토인코더
	- stageIdx, charIdx, maxDamage -> Auto Encoder -> Loss가 확 튐
	- 아까의 가정이 참이어야 이것을 쓸 수 있음
	- 카드 부정사용 오토인코더로 활용하고 있습니다

### 피쳐 엔지니어링은 어떻게 하지?
- Json Raw 데이터
	- 중요한 부분만 뽑아야 함
	- numeric 데이터만 추출
	- 온라인 트레이닝 / 배치 프로세싱 상황에 따라 다름
	- 저장된 로그에 배치 잡을 돌리고
- 실제 필요한 건 데이터 중 일부
	- 모든 데이터를 다 때려넣으면 안됨! 구글만큼 데이터가 많으면 괜찮지만.. 아님 
- 전투 로그가 엄청 많음
- EDA
	- 탐색적 데이터 분석
	- 게임상에 있는 정보를 기반으로 EDA를 해서 게임상에서 일어나는 **특이한 패턴**을 찾는데 주력! 
	- 장비 레벨에 따른 데미지는 상관관계가 있으니 꼭 넣어야 함
	- 이벤트 유무

- 어떤 feature가 상관관계가 높은지 알 수 없음
	- 사후 분석!!
	- 이벤트가 일어난 시점의 데이터를 기준으로 저장해야 합니다

- 로그 디자인
	- JSON이 적당함
	- Nested, Repeated 정보를 어떻게 잘 보관하고 검색할 지 고민
	- 게임은 reward 데이터의 경우 flat하면 개수가 달라짐. Nested한 데이터를 잘 저장하는 DB를 사용해야 함

- 데이터 관리
	- 기존보다 저장 데이터 용량은 더욱 커질 수 밖에 없음
	- 트레이닝 데이터 사이즈를 줄여보는 것도 방법
	- 콜드 데이터는 비용 절감에 주력
	- feature는 최대한 줄이고 차원 축소 

- 데이터 검증
	- 관리할 로그 종류와 데이터 타입이 많아짐
	- 테스트 기반 검증을 진행
	- 모든 로그 데이터는 검증 로직 테스트를 게임 업데이트가 가능하도록 해야 함!

	
- 결론
	- 딥러닝은 모델보다 피쳐가 생명
	- 어떤게 더 중요한지 모르니 모든걸 로그로 쌓고
	- EDA를 빡세게 해서 특이한 패턴을 찾고
	- 그 패턴을 기반으로 모델 학습
	- 데이터는 테스트 기반 validation
	- 계속해서 시도하고 실패하다보면 원하시는 모델이 나옵니다!

 
	 
## 바닥부터 시작하는 데이터 인프라
- 제 발표를 제가 리뷰하는 것은 민망하니 패스
- 많은 분들에게 도움이 되었길..!
- 개인적으론 아쉬움이 남네요(하나의 분야를 자세히 이야기할 걸 그랬나..)

- [발표 자료](https://goo.gl/rYqMPU)

## 게임회사 주니어 웹 개발자가 바라본 데이터 분석 이야기
- 파이썬 덕후인 준범님. 넥슨의 어뷰징 탐지 팀!
- 개인적으로 가지고 있는 프로젝트에서 절대 터질 일 없는 슬픈 서버를 가지고 있었음

### 게임 회사의 데이터
- 로그가 엄청 많음!
- IDC, AWS

### 대시보드를 만들어 주세요!
- R Shiny를 사용하고 있음
- 장고를 사용해 만들었는데 대시보드가 느리다는 클레임이 들어옴
	- 로그 저장소(JSON, Parquet) -> MySQL Aurora에 데이터 적재
	- Query Optimize를 했는데 엄청 느림. 그래서 날 쿼리로 작성해봄
	- 날 쿼리도 느림ㅠㅠ
- 인덱스는 타고 있는가?
	- Table Full-SCAN을 하고 있네????
- MySQL 등 DB는 인덱스가 있더라도 내장 Optimizer가 인덱스를 무시
	- 강제로 인덱스를 타게 하면 더 빨라질까?
	
### 데이터 분석 Task 할당
- 어뷰징 탐지팀인데 어뷰징은 무엇인가?
	- Fair-Play를 해치는 경우
	- 가계정을 만들어 랜덤 상자 부정 획득
	- 다른 유저의 계정 도용
	- 작업장
	- 핵
- 장르까지 고려 	
- PC / Mobile 등의 플레이 환경


### 게임 특성에 따라 조금씩 다른 판별기준
- 평소와 다른 장소에서 로그인했다면?
	- PC 게임인데 지역이 달라짐 -> 뭔가 이상한데?
		- 추석이면 인정
	- 모바일이면 이동할 때마다 위치가 바뀌므로 정상 
- 게임 장르별 다른 어뷰징
	- MMORPG
	- FPS  
- 게임 하나하나에 특화된 모델을 만드느냐 vs 비슷한 장르에 확장 가능한 모델을 만드느냐

### 정량적 접근 vs 정성적 접근
- Case 1. 신규 게임이 오픈했는데 어뷰저가 있는 듯?
	- 횡적으로 확장 가능한 모델을 사용
	- 접속기록, 계정명, 플레이타임 등 모든 게임에 다 남고있는 것을 참고
	- 너무 뻔한 어뷰징은 제거
	- 공통 형식으로 로그가 남고 있어서 그대로 적용 가능
	- 이것만으로는 모두 잡을 수는 없음
- Case 2. 게임 특성에 대한 이해
	- 어떤 어뷰징이 있는지 모르겠음!
	- 매크로 핵 커뮤니티가 존재하나 등업하기엔 어려움
	- 유저들의 신고를 참고해서 유추
	
### 진행 과정	
- json, parquet을 사용
	- for문을 사용해서 함수를 적용하니 작업이 안끝나....
- 분산 처리를 해보자!
	- Spark, EMR, Cluster
	- PySpark
- 수많은 로그에서 찾아내는 인사이트는 아니었음

### AWS Lambda와 함께한 서버리스 아키텍처
- 모델의 크기가 제약


## 패널 토크
<img src="https://www.dropbox.com/s/lnll7w28ifbao6n/IMG_2139.JPG?raw=1">

윤진석님, 김진중님, 나해빈님, 이문수님과 발표자분들의 패널 토크 시간이었습니다  
살다보니 제가 패널 토크도 해보네요.. 생각보다 1시간이 짧았습니다. 데이터 관련 이런 저런 이야기를 주고 받는 시간이었습니다!!!  


## 네트워킹
자리에 계신 분들이랑 많은 대화를 나누었습니다. 도메인이 다른 분들이랑 대화하는 것은 항상 즐거운 것 같아요! 게다가 페이스북에서만 봤던 분들을 직접 볼 수 있어서 정말 좋았습니다 :)



## 후기
발표자로 참여했지만 다른 분들의 이야기가 정말 흥미로웠습니다! 앞으로도 이런 행사가 자주 생기면 좋겠습니다!!! 제플 짱 >_</  
후드 진짜 이뻐서 동네 독서실에서 돌아다닐 때 맨날 입고다닐 것 같아요!!!
