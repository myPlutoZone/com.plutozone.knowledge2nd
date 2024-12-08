# com.plutozone.knowledge.middleware.Jenkins


## Developer + GitLab + Jenkins + Registry(=hub.docker.com)
1. Developer is uploadding code at GitLab
2. Jenkins is pushing at Registry and testing for development or staging or product
3. Kurbernates is upload at development or staging or product


## Enviroments
1. Virtual Box
2. Rocky 9.5(rockylinux.org vs. mirror.navercorp.com) at Virtual Box
3. mobaxterm + https://codeshare.io/


## Docker Repositoy 설정 및 설치 그리고 Run Container(ngnix) as root at Rocky
```bash
$ curl -o /etc/yum.repos.d/docker-ce.repo https://download.docker.com/linux/centos/docker-ce.repo
$ dnf install -y docker-ce
$ systemctl start docker                       # Run Docker
$ systemctl enable docker                      # For Auto Run
$ docker run -d quay.io/uvelyster/nginx        # Download and run container(nginx at quay.io)
$ docker ps
$ docker images
$ docker inspect [IDs]
$ curl 172.17.0.2                              # Default Docker Network: 172.17.0.1/16
```


## Manage Docker Image by Auto(=Dockerfile) and Container
```bash
$ mkdir build
$ cd build
$ vi Dockerfile
FROM quay.io/uvelyster/busybox
CMD echo helloworld
$ docker build .
$ docker images
$ docker run [Name or IDs]                 # print helloworld
$ docker run [Name or IDs] echo itworks    # print itworks
$ docker tag [Name or IDs] test_hello
$ docker tag test_hello test
$ docker images
$ docker rmi test_hello                    # remove tag or image
$ docker rmi test                          # remove tag or image but ERROR because Container is running
$ docer ps -a
$ docker rm [Name or IDs]                  # remove a container
$ docker rm -f $(docker ps -aq)            # remove all container
$ docker rmi test                          # remove tag or image
```


## Instruction
```bash
$ vi Dockerfile
FROM quay.io/uvelyster/nginx                                # FROM
RUN echo 'helloworld' > /usr/share/nginx/html/index.html    # 컨테이너 설치(RUN) 시 동작할 명령어
ENTRYPOINT ["/docker-entrypoint.sh"]                        # ENTRYPOINT
CMD ["nginx", "-g", "daemon off;"]                          # 컨테이너 실행(CMD) 시 동작할 명령어: ENTRYPOINT + CMD
$ docker build -t test_nginx .                              # Tag name is test_nginx
$ docker run -d test_nginx
$ curl 172.17.0.2
$ vi Dockerfile
FROM quay.io/uvelyster/ubuntu:24.04                         
RUN apt-get update; apt-get install -y apache2              # 컨테이너 설치(RUN) 시 동작할 명령어
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]            # 컨테이너 실행(CMD) 시 동작할 명령어: CMD
$ docker build -t test_apache .
$ docker run -d test_apache
$ curl 172.17.0.3
$ vi index.html
$ vi Dockerfile
FROM quay.io/uvelyster/ubuntu:24.04
RUN apt-get update; apt-get install -y apache2
COPY index.html /var/www/html/index.html                    # COPY(로컬의 파일 복사) vs. ADD(네트워크/압축 파일 등 추가)
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
$ docker build -t test_apache:v2 .                          # 버전 정보
$ docker run -d test_apache:v2
$ curl 172.17.0.4
# docker exec [Name or IDs] env                             # Container ENV(실행 시 파라미터) 확인 vs. ARG(빌드 시 파라미터)
$ vi Dockerfile
FROM quay.io/uvelyster/ubuntu:24.04
RUN apt-get update; apt-get install -y apache2
COPY index.html /var/www/html/index.html
EXPOSE 80                                                   # EXPOSE(HTTP: 80)
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
$ docker build -t test_apache:v3 .
$ docker run -d test_apache:v3
$ curl 172.17.0.5
$ docker run -d -P test_apache:v3                            # 임의 포트 포워딩(localhost:* > container: EXPOSE)
$ docker run -d -P 1234:80 test_apache:v3                    # 지정 포트 포워딩(localhost:1234 > container: 80)
$ vi Dockerfile
FROM quay.io/uvelyster/ubuntu:24.04
RUN apt-get update; apt-get install -y apache2
EXPOSE 80
VOLUME /var/www/html                                         # VOLUME for Container
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
$ docker build -t test_apache:v4 .
$ docker run -d test_apache:v4
$ docker volume ls
$ cd /var/lib/docker/volumes/%VOLUME_ID%/_data/
$ docker inspect [Name or IDs]
$ curl 172.17.0.6
$ echo helloworld > index.html
$ curl 172.17.0.6
# 기타: USER(계정), ARG(빌드 시 파라미터) vs. ENV(실행 시 파라미터), ONBUILD(=트리거)
```


## Registry(docker.io for Public or Private vs. quay.io, harbor for Public and Private)
```bash
$ docker search ubuntu                                                 # Search ubuntu at docker.io/library(=offical)
$ docker pull ubuntu                                                   # Pull ubuntu at docker.io/library(=offical)

$ docker tag quay.io/uvelyster/ubuntu:24.04 ID/ubuntu_24.04            # Select User Image
$ docker login -u ID                                                   # Login in hub.docker.com
$ docker push ID/ubuntu_24.04                                          # Push User Image(docker.io/ID/ubuntu_24.04)
$ docker rmi ID/ubuntu_24.04
$ docker run -d ID/ubuntu_24.04                                        # Download User Image and run container
```


## Install Harbor for Private and Manage Image
```bash
$ mkdir /data
# 인증서 생성
$ openssl \
req -newkey rsa:4096 -nodes -sha256 -x509 \
-days 365 -keyout /data/myregistry.com.key -out /data/myregistry.com.crt \
-subj '/CN=myregistry.com' \
-addext "subjectAltName = DNS:myregistry.com"
$ mkdir -p /etc/docker/certs.d/myregistry.com
$ cp /data/myregistry.com.crt /etc/docker/certs.d/myregistry.com/ca.crt
$ dnf install -y wget
$ wget https://github.com/goharbor/harbor/releases/download/v2.10.0/harbor-offline-installer-v2.10.0.tgz
$ tar xvzf harbor-offline-installer-v2.10.0.tgz
$ cd harbor
$ cp harbor.yml.tmpl harbor.yml
$ vi harbor.yml
hostname: muregistry.com
certificate: /data/muregistry.com.crt
private_key: /data/muregistry.com.key
harbor_admin_password: Harbor12345
$ vi /etc/hosts
172.16.0.200  myregistry.com
$ ./install.sh
# https://172.16.0.200 접속
$ docker compose down                                                  # harbor 삭제 by docker-compose.yml
$ ./install                                                            # =docker compose up
$ docker tag quay.io/uvelyster/nginx myregistry.com/library/nginx      # Create Image, Login and Push
$ docker login myregistry.com
$ docker push myregistry.com/library/nginx
```


## Jenkins Installation
```bash
$ docker run -d --name jenkins \
--restart always \
--dns 172.16.0.200 \
-p 8080:8080 \
-p 50000:50000 \
-v jenkins_home:/var/jenkins_home \
-v /var/run/docker.sock:/var/run/docker.sock \
quay.io/uvelyster/jenkins
$ docker logs jenkins                 # Find Password
$ dnf install -y dnsmasq              # 필요 시 DNS 설치
$ systemctl start dnsmasq
$ systemctl enable dnsmasq
# http://172.16.0.200:8080/
$ vi /etc/dnsmasq.conf                # 필요 시 DNS 재설정 for Loopback Disable
# interface=lo
$ systemctl restart dnsmasq
# http://172.16.0.200:8080/ and Create First Admin User
```


## Jenkins Configuration
- 새로운 Item
- Jenkins 관리
  - System Configuration
    - System
    - Tools
    - Plugins
    - Nodes vs. Clouds(Docker 등)
  - Security
    - Security
    - Credentials
    - Credential Providers
    - Users

### 새로운 Item 등록
1. Input Name and Select Item Type(=Pipeline)
2. Input Description and Script
```bash
pipeline {
    agent any
    stages {
        stage('hello') {
            steps {
                echo 'helloworld'
            }
        }
    }
}
```
3. Select Item(=Pipeline) and 지금 빌드
4. Select Build History and Console Output 등에서 결과를 확인
5. Select Item(=Pipeline) 삭제

### Clouds에 Docker 등록
1. Select Clouds and Plugins
2. Install Docker and Reselect Clouds
3. Select New Cloud and Input Name
```bash
# Docker Port 설정
$ systemctl status docker
$ vi /usr/lib/systemd/system/docker.service
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2379 --containerd=/run/containerd/containerd.sock    # -H tcp://0.0.0.:2379를 추가
$ systemctl daemon-reload
$ systemctl restart docker
```
4. Configuratin Docker Cloud details(Docker Host URI=tcp://172.16.0.200:2379 등) and Docker Agent templates(Maven 정보)
5. Pull maven
```bash
$ docker pull maven
```
6. ...
7. Input Name and Select Item Type(=Pipeline)
8. Input Description and Script
```bash
pipeline {
    agent {
        node {
            label 'docker'
        }
    }
    stages {
        stage('hello') {
            steps {
                echo 'helloworld'
            }
        }
    }
}
```

### Jenkins Sciprt for Build Farm(maven, gradle, python agent) at Jenkins vs. Jenkinsfile at Source(include Web Hook)


## GitLab Installation
```bash
$ cd ~
$ mkdir gitlab
$ cd gitlab
$ vi docker-compose.yaml
services:
  gitlab:
    image: 'quay.io/uvelyster/gitlab-ce:latest'
    restart: always
    hostname: 'mygitlab.com'
    container_name: gitlab
    dns: 172.16.0.200
    environment:
      GITLAB_ROOT_PASSWORD: P@ssw0rd
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://mygitlab.com'
        registry_external_url 'https://myregistry.com'
    ports:
      - '80:80'
      - '443:443'
    volumes:
      - '/root/gitlab/config:/etc/gitlab'
      - '/data:/etc/gitlab/ssl'
      - '/root/gitlab/logs:/var/log/gitlab'
      - '/root/gitlab/data:/var/opt/gitlab'
      - '/root/gitlab/backup:/var/opt/gitlab/backups'
      - '/root/gitlab/registry:/var/opt/gitlab/gitlab-rails/shared/registry'
$ docker compose up -d
$ vi /etc/hosts
$ 172.16.0.200	myregistry.com mygitlab.com
```

### Git Client Usage
```bash
$ git config -l

$ git config --global user.name "root"
$ git config --global user.email "root@mygitlab.com"

$ git init --initial-branch=main
$ git remote add origin http://mygitlab.com/root/test.git

$ git add .
$ git commit -m 'message'
$ git push -u origin main
```
