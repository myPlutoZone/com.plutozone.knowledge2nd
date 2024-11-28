# com.plutozone.knowledge.language.Python


## Installation
- pip
```cmd
# pip list						# 설치된 목록 보기
# pip install LIBRARY			# 라이브러리 설치
# pip install beautifulsoup4
# pip install selenium			# Firefox headless와 geckodriver가 자동 설치됨(사전에 하위 버전의 FireFox 설치 권장)
# pip install openpyxl
# pip install jupyter			# C:\>jupyter notebook(웹 브라우저에서 실행되는 대화형 파이썬 환경)
```

- Colab(코랩: https://colab.research.google.com, 구글이 대화식 개발(파이션 등) 환경인 Jupyter(https://jupyter.org)를 커스터마이징하여 온라인으로 제공)
	- 노트북(Notebook)은 Jupyter 프로젝트의 대표적인 기능(제품)이며 코랩 노트북은 구글 클라우드의 가상 서버를 사용
	- 노트북(Notebook) = 텍스트 셀(Text Cell) + 코드 셀(Code Cell)


## Library
- NumPy(넘파이, Numerical Python)
	- C 언어로 구현된 숫자, 배열 등 수학 계산에 유용
	- NumPy 배열의 차원(Dimension)
		- Scalar(스칼라)는 0차원 배열
		- Vector(벡터)는 1차원 배열
		- Matrix(행렬)는 2차원 배열
		- Tensor(텐서)는 다차원 배열(1/2차원 포함)
	- 배열의 랭크(Rank)는 차원(Dimension)의 크기이고 모양(Shape)는 크기를 의미(예: 3행 4열의 2차원 배열은 Rank[2]이고 Shape[3,4])
- Pandas(판다스)
	- Data Frame(데이터 프레임) 및 Series(시리즈) 데이터(대용량 테이블 형태) 분석에 특화


## Grammar
- os(운영체제에서 제공되는 여러 기능을 다룰 수 있는 파이썬 모듈) 모듈의 listdir(현재 경로의 파일 또는 폴더의 리스트를 반환하는 함수) 함수를 사용 방법들
	- Case 1. import os
		- 현재 python 파일에서 listdir 함수를 사용 하려면 os.listdir( )이라고 입력
	- Case 2. from os import *
		- 현재 python 파일에서 listdir 함수를 사용하려면 listdir( )만 입력
		- 이때 주의할 점은 from으로 불러온 모듈에 같은 이름의 함수가 있으면 문제가 발생
		- 참고로 import *를 와일드 임포트(wild import)
	- Case 3. from os import listdir
		- 하나의 함수만 가져오는 것도 가능(함수 사용법은 Case 2.와 같음)
		- 와일드 임포트는 뜻하지 않게 기존의 변수나 함수를 덮어 쓸 때가 있을 수 있으므로 해당 방법이 바람직