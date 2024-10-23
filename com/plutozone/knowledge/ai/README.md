# com.plutozone.knowledge.AI
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

- 알고리즘
	- 문장 생성
		- 마르코프 체인(워드 샐러드): 무작위 확률 기반
		- LSTM(Long Short Term-Memory) 또는 RNN(Recurrent Neural Network): 러닝머신 기반으로 다음에 위치할 문장을 예측

- Reference
	- https://chat.openai.com/auth/login
	- https://platform.openai.com/
	- https://bard.google.com/
	- https://learn.microsoft.com/ko-kr/azure/ai-services/openai/chatgpt-quickstart?tabs=command-line%2Cpython&pivots=programming-language-studio
	- https://clova-x.naver.com/