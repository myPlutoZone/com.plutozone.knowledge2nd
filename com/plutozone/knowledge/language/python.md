# com.plutozone.knowledge.language.Python


## Installation
- pip
```cmd
# pip list						# 설치된 라이브러리 목록
# pip install beautifulsoup4	# 라이브러리 설치(예: Beautiful Soup)
# pip install openpyxl			# 라이브러리 설치(예: Openpyxl)
# pip install selenium			# Firefox headless와 geckodriver가 자동 설치됨(사전에 하위 버전의 FireFox 설치 권장)
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
- None and pass
```py
def fn_None():
	# 오류 방지를 위한 임의 객체
	return None

def fn_pass():
	# 오류 방지를 위한 임의 코드
	pass
```

- import
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

- exception
```py
for i in range(10):
	try:
		result = 10 / i
	except ZeroDivisionError:
		print("Not 0")
	else:
		print(result)
	finally:
		print("Finally")

def get_binary_number(decimal: int) -> str:
	assert isinstance(decimal, int) and decimal >= 0, "Input must be a positive integer"
	return bin(decimal)

print(get_binary_number(10))
print(get_binary_number("10"))
print(get_binary_number(-10))
print(get_binary_number(10))
```

- files
```py
# Read file(반드시 close)
file = open("yesterday.txt", "r")
yesterday_lyric = file.readlines()
print(yesterday_lyric)
file.close()

# Read file(with 문으로 작성하면 자동 close)
with open("yesterday.txt", "r") as file:
	yesterday_lyric = file.readlines()
	print(yesterday_lyric)


# Read CSV file
import csv

with open("./csv/test.csv", "r") as file:
	reader = csv.reader(file)
	for row in reader:
		print(row)


# Create a Dictionary Object(=JSON) for JSON & Pickle file
data = {
	"name": "Sungwan Myung"
	, "age": 20
	, "city": "Seoul"
	, "email": ["plutomsw@gmail.com", "plutomsw@naver.com"]
}


# Create & Load a JSON file
import json

with open("data.json", "w") as file:
	json.dump(data, file)

with open("data.json", "r") as file:
	data = json.load(file)
	print(data)
	print(type(data))


# Create & Load a Pickle file
import pickle


with open("data.pickle", "wb") as file:
	pickle.dump(data, file)

with open("data.pickle", "rb") as file:
	data = pickle.load(file)
	print(data)
	print(type(data))
```


## Pandas
```py
```

## Example
- 딕션너리 데이터 입력(이름:key, 전화번호:value)
```py
contacts = dict()

def insertDic():
	while True:
		name = input("이름을 입력하시오: ")
		if name == "":
			print("프로그램을 종료합니다.")
			break

		phone = input("전화번호를 입력하시오: ")
		contacts[name] = phone

insertDic()
print(contacts)
```

- 클래스 기반으로 최고 성적자 확인
```py
class Student:
	def __init__(self, name, age, score):
		self.name	= name
		self.age	= age
		self.score	= score

student_list = []

def insertStudent(name: str, age: int, score: float) -> None:
	student = Student(name, age, score)
	student_list.append(student)

def scoreTop() -> str:
	score_list	= []
	name_list	= []

	for student in student_list:
		score_list.append(student.score)
		name_list.append(student.name)

	max_score = max(score_list)
	max_index = score_list.index(max_score)
	return name_list[max_index]

insertStudent("유관순", 16, 88)
insertStudent("이순신", 45, 95)
insertStudent("김유신", 55, 85)
print("최고점자: ", scoreTop())
```

- 회문 여부
```py
def palindrome(s: str) -> bool:
	"""
	회문이란?
	회문(Palindrome)은 앞으로 읽으나 뒤로 읽으나 같은 단어나 문장을 말한다.
	예를 들어서 "level", "SOS"와 같은 것들이 회문이다.
	"""
	return s == s[::-1]

s = "level"
print(palindrome(s))
```

- random 모듈을 활용하여 알파벳과 숫자 조합의 8자리 문자열 생성
```py
import random

def randomEight():
	alphabet	= [chr(i) for i in range(ord("A"), ord("Z") + 1)] + [chr(i) for i in range(ord("a"), ord("z") + 1)]
	number		= [str(i) for i in range(10)]
	password	= random.choices(alphabet + number, k=8)
	return "".join(password)

print(randomEight())
```

- 4자리 정수를 입력 받아 자리 수의 합 확인(예: 1234를 입력하면 1+2+3+4를 계산 및 반환)
```py
def sumFour(num: int) -> int:
	assert num >= 1000 and num <= 9999
	str_num		= list(str(num))			# ['1', '2', '3', '4']
	list_num	= list(map(int, str_num))	# [1, 2, 3, 4]
	return sum(list_num)

num = int(input("4자리 정수: "))
print("자리수의 합: ", sumFour(num))
```