# com.plutozone.knowledge.middleware.Kubernetes


## Software
- Virtual Box and 4VM(1 for Docker + 3 for Kubernetes Cluster)
- Rocky
- MobaXterm
- Visual Studio Code for Yaml and Extentions(Kubernetes + Kubernetes Support + Remote SSH)


## Overview(https://kubernetes.io/ko/docs/concepts/overview/)
- Container Cluster = Container Orchestrator = Kubernetes
- Kubernetes(k8s)
- control-plane=master
- CNCF(https://landscape.cncf.io/)


## Installation
### master@192.168.56.10 + node1@192.168.56.11 + node2@192.168.56.12 by Kubeadm
#### config and install Containerd at master, node1, node2
```bash
# config enviroment
$ yum -y update
$ timedatectl set-timezone Asia/Seoul
$ cat << EOF >> /etc/hosts
192.168.56.10 master
192.168.56.11 node1
192.168.56.12 node2
EOF
$ systemctl stop firewalld && systemctl disable firewalld            # disable Firewall
$ swapoff -a && sed -i '/ swap / s/^/#/' /etc/fstab                  # disable Swap
$ cat <<EOF |tee /etc/modules-load.d/k8s.conf                        # run automatically Kernal Modules(overlay와 br_netfilter) for Kubernetes
overlay
br_netfilter
EOF
$ modprobe overlay
$ modprobe br_netfilte
$ cat <<EOF |tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1            # config bridge iptable
net.bridge.bridge-nf-call-ip6tables = 1            # support IPv6
net.ipv4.ip_forward                 = 1            # enable IP forware
EOF
$ sysctl --system

# install Containerd(vs. Docker)
$ yum install -y yum-utils
$ yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
$ yum install -y containerd.io
$ systemctl daemon-reload
$ systemctl enable --now containerd

# config Containerd for Kubernetes
$ containerd config default > /etc/containerd/config.toml
$ sed -i 's/ SystemdCgroup = false/ SystemdCgroup = true/' /etc/containerd/config.toml
$ systemctl restart containerd
```

#### install Kubernetes at master, node1, node2
```bash
$ cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF
$ setenforce 0
$ sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
$ sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
```

#### config Kubernetes Cluster `at only Master`
```bash
# Node Join Command(include Token) for nodes
$ kubeadm init --pod-network-cidr=192.168.0.0/16 --apiserver-advertise-address 192.168.56.10

$ mkdir -p $HOME/.kube
$ cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ chown $(id -u):$(id -g) $HOME/.kube/config

# install CNI
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.1/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.1/manifests/custom-resources.yaml

# [OPTION] recreate Token for nodes
$ kubeadm token create --print-join-command
```

#### Node Join at only node1, node2
```bash
# EX) kubeadm join <control-plane-host>:<port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

#### install Tools for Kubernetes `at only Master`
```bash
$ yum -y install bash-completion
$ cd ~
$ echo "source <(kubectl completion bash)" >> ~/.bashrc
$ echo 'alias k=kubectl' >>~/.bashrc
$ echo 'complete -o default -F __start_kubectl k' >>~/.bashrc
$ source ~/.bashrc
```

#### Confirm at Master
```bash
$ kubectl get node
```

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


## Client at Windows
### install Kube Command(kubectl)
- https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-windows/#install-nonstandard-package-tools
- copy to %USER%.kube\config from ~/.kube/config
- C:\kubectl get node

### create YAML
```cmd
C:\k8s\pods\type pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
  labels:
    name: myapp
spec:
  containers:
  - name: myapp
    image: [ACCOUNT]/[REPOSITORY]:[TAG]
    ports:
      - containerPort: 5000

# Create POD by YAML
C:\k8s\pods\kubectl apply -f .\pod.yaml
C:\k8s\pods\kubectl get pod

# Create POD by kubectl
C:\k8s\pods\kubectl run nginx --image=nginx --labels=app=nginx
C:\k8s\pods\kubectl get pod
C:\k8s\pods\kubectl get pod -o wide

# Delete POD
C:\k8s\pods\kubectl delete pod [NAME]                                        # kubectl delete pod nginx
C:\k8s\pods\kubectl delete pod --all

# Expose POD
C:\k8s\pods\type pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mysys
  labels:
    name: mysys
spec:
  containers:
  - name: mysys
    image: [ACCOUNT]/[REPOSITORY]:[TAG]
    ports:
      - containerPort: 5000
C:\k8s\pods\kubectl apply -f .\pod.yaml                                                        # EX) name=mysys
C:\k8s\pods\kubectl expose pod [NAME_POD] --name [NAME_SERVICE] --port 80                      # EX) kubectl expose pod mysys --name mysys-srv --port 80
C:\k8s\pods\kubectl get svc
C:\k8s\pods\kubectl expose pod [NAME_POD] --name [NAME_SERVICE] --port 5000 --type NodePort    # EX) kubectl expose pod mysys --name mysys-srv2nd --port 5000 --type NodePort
C:\k8s\pods\kubectl get svc
C:\k8s\pods\kubectl delete svc [NAME_SERVICE]
C:\k8s\pods\kubectl expose pod [NAME_POD] --name [NAME_SERVICE] --port 80 --type LoadBalancer  # EX) kubectl expose pod mysys --name mysys-srv3rd --port 80 --type LoadBalancer
C:\k8s\pods\kubectl get svc
```

### config Terminal(docker) for Windows
```bash
# create Key Pair(RSA)
$ yum install -y tar
$ ssh-keygen -t rsa                    # generate private and public key
$ cp id_rsa.pub authorized_keys        # copy for sshd_config at /etc/ssh/sshd_config

# install Kubernetes Control
$ cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF
$ setenforce 0
$ sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
$ sudo yum install -y kubectl --disableexcludes=kubernetes

# Copy Certification
$ mkdir .kube
$ scp root@192.168.56.10:/root/.kube/config .
```

### config for Windows
- copy to %USER%.ssh\ from ~/.ssh/id_rsa    # copy private key
```cmd
C:\ssh ID@192.168.56.100
```
- config SSH at Visual Studio Code
- install Extenstion(Kubenetes, Kubenetes Support) for Remote SSH


## NONE Ready(reset, rm and reconfig)
```bash
$ kubeadm reset		# at master/node1/node2
$ rm -Ff .kube		# at master
```
