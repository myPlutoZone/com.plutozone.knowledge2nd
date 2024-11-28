# com.plutozone.knowledge.AI


## Fundamental & Term
- Colab(코랩: https://colab.research.google.com, 구글이 대화식 개발(파이션 등) 환경인 Jupyter(https://jupyter.org)를 커스터마이징하여 온라인으로 제공)
	- 노트북(Notebook)은 Jupyter 프로젝트의 대표적인 기능(제품)이며 코랩 노트북은 구글 클라우드의 가상 서버를 사용
	- 노트북(Notebook) = 텍스트 셀(Text Cell) + 코드 셀(Code Cell)
- AI(인공 지능)
	- 일반인공지능 or 강인공지능=The Terminator
	- 약인공지능=Char GPT
- 학습의 종류
	- 지도 학습: 입력(데이터)과 타켓(답)을 전달하여 모델을 훈련하여 새로운(테스트) 입력(데이터)의 타겟(답)을 분류 또는 예측(예: 길이와 무게에 따른 어류 확인 등, 손 글씨 이미지와 글자에 따른 글자 확인 등)
	- 비지도 학습: 타켓(답)이 없이 입력(데이터)만 제공하여 다른 데이터의 규칙성 확인(무엇을 분류 또는 예측하는 것이 아니라 입력 데이터에서 어떤 특징을 찾는데 주로 활용)
	- 강화 학습(알파고 등): 부분적 답을 제공하여 데이터를 기반으로 최적의 답을 확인
- ML(Machine Learning, 머신러닝)
	- 수 많은 데이터를 학습 시켜서 패턴을 찾아 내고 패턴을 기반으로 데이터를 **분류**하고 **예측**하는 것(예: 크기, 색깔 등에 따른 독버섯 구분)
	- 대표적인 ML 라이브러리는 Scikit-Learn(사이킷런=구글에서 개발한 머신러닝 프레임워크)
- DL(Deep Learning, 딥러닝)
	- ML 알고리즘 중에서 인공 신경망을 기반으로 한 방법들을 통칭
	- 러닝머신에서는 특성을 직접 지정하지만 딥러닝에서는 학습 데이터를 통해 자동으로 특성을 추출(예: 사과와 포도를 판별하는 경우 머신러닝에서는 특성으로 색을 지정했다면 러닝머신에는 대량의 데이터 학습을 통해 자동으로 찾아서 색이 될 수도 모양이 될 수도 있음)
	- 대표적인 DL 라이브러리는 TensorFlow(텐서플로 by 구글)와 PyTorch(파이토치 by 페이스북)
	- C/C++ 언어로 구현된 TensorFlow는 구글이 오픈 소스(Apache 2.0 라이센스 기반으로 상업적인 용도로 사용 가능)로 공개한 딥러닝 알고리즘으로 대규모 숫자 계산 등을 지원
	- 케라스(Keras)는 TensorFlow, Theano, CNTK, PyTorch 등 딥 러닝 라이브러리를 백엔드로 사용하여 쉽게 다층 퍼셉트론 신경망 모델, 컨볼루션 신경망 모델, 순환 신경망 모델, 조합 모델 등을 구성 가능


## ML(Machine Learning, 머신러닝)
- 데이더 종류 for ML, DL and AI
	- Scalar(스칼라)
	- Vector(벡터)
	- Matrix(행렬) and Tensor(텐서)
- Feature(특성)은 데이터를 표현하는 하나의 성질
- Training(훈련)은 알고리즘이 데이터에서 규칙을 찾는 과정
	- Training Set(훈련 세트)
	- Test Set(테스트 세트)
- 알고리즘(Algorism)
	- Classification(분류): 샘플을 몇 개의 클래스 중 하나로 분류하는 문제
	- Regression(회귀): 클래스 중 하나로 분류하는 것이 아니라 상관 관계를 분석하여 임의의 어떤 숫자를 예측하는 문제(예: 내년도 경제 성장를 예측하거나 배달이 도착하는 시간 예측하는 것처럼 정해진 클래스가 없고 임의의 수치를 출력)
- 과대적합(overfitting) vs. 과소적합(underfitting)
- 샘플링 편향(=잘못된 훈련 세트)
- 데이터 전처리
	- 특성의 스케일, 결측치, 이상치, 표준화와 정규화 등
	- 표준점수(특성의 스케일을 조정하는 대표적인 방법) = (데이터 - 평균) / 표준편차
- Model(모델, 알고리즘을 구현한 프로그램 또는 알고리즘 자체)과 Accuracy(정확도)
- Classification(분류)는 여러 종류(Class, 클래스)중에서 하나를 구별해 내는 문제이며 2개의 클래스 중 하나를 고르는 문제는 Binary Classification(이진 분류)
- Linear(선형)
- 교차 검증과 그리드 서치
	- 훈련 후 테스트가 아닌 정밀도 확보를 위해 훈련 후 검증을 거쳐 테스트 진행
	- 교차 검증(Cross Validation) 방법에는 K 분할 교차 검증(K-Fold Cross Validation) 등이 있다.
	- 그리드 서치는 어떤 매개변수가 적절한지 자동으로 조사하는 방법


## 주요 알고리즘(Algorism)
- ML
	- K-NN(K-Nearest Neighbor: K-최근접 이웃)
	- 로지스틱 회귀(인공 신경망 알고리즘의 기초로 사용됨)
		- 확률(=랜덤 또는 무작위)적 경사 하강법(경사를 내려가는 방법)=점진적 또는 온라인 학습
	- 트리
		- 결정 트리
			- 랜덤 포레스트(Random Forest, Randomized Trees) at 사이킷런: 집단 학습을 기반으로 고수준의 분류, 회귀 등을 구현
- DL
	- 이미지
		- Average Hash: 이미지를 리사이즈하고 이진화 및 해쉬화(예: 이미지 패턴화, 동일 이미지 찾기 등)
		- CNN(Convolution Neural Network, 합성곱 신경망): 신경망의 단점(층이 늘어날 경우 학습에 문제 발생)을 입력층과 출력층 사이에 합성곱층과 풀링층을 넣어 보완한 딥러닝 방법중 하나
		- OpenCV(Open Source Computer Vision Library)
	- 텍스트
		- 마르코프 체인(워드 샐러드): 무작위 확률 기반으로 문장 생성(예: ChatBot 등)
		- LSTM(Long Short Term-Memory) 또는 RNN(Recurrent Neural Network): ML 기반으로 다음에 위치할 문장을 예측하여 생성


## Demo
- ChatBot by Python(ChatBot.zip)
```bash
# 폴더 및 파일 생성
$ cgi-bin/chatbot.py		# 챗봇 웹 화면
$ cgi-bin/botengine.py		# 챗봇 엔진(마르코프 체인 기반)

# 실행 권한 부여 및 웹 서버 실행
$ chmod +x cgi-bin/chatbot.py
$ python -m http.server --cgi 8080

# 브라우저에서 실행
# http://localhost:8080/cgi-bin/chatbot.py
```


## Reference
- https://chat.openai.com/auth/login
- https://platform.openai.com/
- https://bard.google.com/
- https://learn.microsoft.com/ko-kr/azure/ai-services/openai/chatgpt-quickstart?tabs=command-line%2Cpython&pivots=programming-language-studio
- https://clova-x.naver.com/
