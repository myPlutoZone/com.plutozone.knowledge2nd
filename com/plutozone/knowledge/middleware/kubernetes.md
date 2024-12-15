# com.plutozone.knowledge.middleware.Kubernetes


## Software
- Virtual Box and 4VM(1 for Docker + 3 for Kubernetes Cluster)
- Rocky
- MobaXterm
- Visual Studio Code for Yaml and Extentions(Kubernetes + Kubernetes Support + Remote SSH)


## Overview at https://kubernetes.io/ko/docs/concepts/overview/
- Container Cluster = Container Orchestrator = Kubernetes
- Kubernetes(k8s)
- control-plane=master


## Installation
### master@192.168.56.10 + node1@192.168.56.11 + node2@192.168.56.12

### docker@192.168.56.100
- install Docker at Rocky or Ubuntu
- create REPOSITORY(EX: app) at hub.docker.com
- build image and push
```bash
$ cd ~
$ mkdir build
$ cd build
$ vi app.py
from flask import Flask, request
import socket

app = Flask(__name__)

@app.route('/')
def index():
    # 클라이언트 IP 주소 추출
    client_ip = request.remote_addr
    xff_header = request.headers.get('X-Forwarded-For', None)
    if xff_header:
        # X-Forwarded-For 헤더에서 첫 번째 IP 주소 추출
        client_ip = xff_header.split(',')[0].strip()

    client_user_agent = request.headers.get('User-Agent')

    # 서버 정보
    server_ip = socket.gethostbyname(socket.gethostname())
    server_name = socket.gethostname()

    return f"""
    <html>
    <head>
        <style>
            .client-info {{
                color: blue;
            }}
            .server-info {{
                color: green;
            }}
        </style>
    </head>
    <body>
        <h1> - 접속 정보 Version 1 - </h1>

        <h2 class="client-info">1. 클라이언트 정보</h2>
        <p class="client-info">IP 주소: {client_ip}</p>
        <p class="client-info">X-Forwarded-For: {xff_header if xff_header else '없음'}</p>
        <p class="client-info">User-Agent: {client_user_agent}</p>

        <h2 class="server-info">2. 서버 정보</h2>
        <p class="server-info">IP 주소: {server_ip}</p>
        <p class="server-info">이름: {server_name}</p>
    </body>
    </html>
    """

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
$ vi Dockerfile
# 베이스 이미지
FROM python:3.8

# 작업 디렉토리 설정
WORKDIR /app

# 종속성 파일 복사 및 설치
RUN pip install flask
# 애플리케이션 코드 복사
COPY app.py .

# 컨테이너 시작 시 실행할 명령어
CMD ["python", "app.py"]
$ docker build -t [ACCOUNT]/[REPOSITORY]:[TAG] .                    # EX) docker build -t ID/app:v1 .
$ docker push [ACCOUNT]/[REPOSITORY]:[TAG]                          # EX) docker push ID/app:v1
$ docker run -d -p 80:5000 --name app [ACCOUNT]/[REPOSITORY]:[TAG]  # EX) docker run -d -p 80:5000 --name app ID/app:v1
$ docker ps -a
- http://192.168.56.100
```

### Kube Command(kubuctl) for Windows
- https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-windows/#install-nonstandard-package-tools
- copy to %USER%.kube\config from ~/.kube/config
- C:\kubectl get node


## NONE Ready(reset, rm and reconfig)
```bash
$ kubeadm reset		# at master/node1/node2
$ rm -Ff .kube		# at master
```
