# com.plutozone.knowledge.middleware.Docker


## Docket container Basic
- Containerization
- Container Basic Lifecycle
- Image Build vs. Source Build
- Network and Storage
- Compose(Docker=Container Engine, Kubernates=Orchestrator=Server Cluster Tool)


## Enviroments
- VM Tools(Virtual Box)
- Linux Distribution(Rocky 9.5)
- Terminal(mobaxterm)
- Zoomit(https://learn.microsoft.com/ko-kr/sysinternals/downloads/zoomit)


## Host Only Network at Virtual Box
- 172.16.0.0/24(24 = 11111111.11111111.11111111.00000000 = 255.255.255.0)
- 172.16.0.101 ~ 254 for DHCP(172.16.0.100)


## Create VM
- 1 vCPU, 2GB, NAT(enp0s3) for External + Host Only(enp0s8, 172.16.0.0/24) for Internal + 20GB/25GB at Rocky/Ubuntu


## What's Container
- Process at Source + Runtime + Enviroment
- Isolation
- Virtualization(base on OS) by Hybervistor vs. Containerization by Container Engine(=Docker)


## Run VM vs. Container(OCI, Open Container Initiative)
- vdi for Oracle
- vmdk for VMware
- qcow2 for OpenSource
- vhd/x for Windows


## Install Docker
- install by root(#) at Rocky 9.5
```bash
$ curl -fsSL https://download.docker.com/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo		
$ yum install -y docker-ce		
$ docker version            # only Client
$ systemctl start docker		# Server Start
$ systemctl enable docker		# start on boot
$ docker version		        # Client and Server Version
$ docker run hello-world		# download and print Hello from Docker!
```

- install at Ubuntu 24.04.1
```bash
$ sudo apt update                                                                                                # update
$ sudo apt install apt-transport-https ca-certificates curl                                                      # install requried package
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -                                   # Docker official GPG Key
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"  # Docker Repository
$ sudo apt update                                                                                                # update
$ sudo apt-get update && sudo apt-get install docker-ce docker-ce-cli containerd.io                              # install Docker
$ sudo docker run hello-world                                                                                    # confirm Docker
$ sudo docker version

# Run without sudo for Docker
$ sudo usermod -aG docker ${USER}
$ sudo shutdown -r now
$ groups
$ docker ps -a
```

## Server & Client
- docker는 Client Tool이므로 Localhost 통신이 기본
- kubernates는 Server Tool이므로 Remote Host 통신이 기본
```bash
$ docker -H 172.16.0.102:2375      # Remote Host 접속 시
```


## Docker Host(=Daemon/Server) 구성
- Containers(Thin Read/Write)
- Images(Read Only + Version 및 Source, Runtime 등으로 구분됨)
- Registry(Default: hub.docker.com)


## Command
- Search Image at Registry
```bash
$ docker search nginx				        # Registry에서 nginx Image를 검색(=https://hub.docker.com/에서 nginx를 검색)	
$ docker search quay.io/nginx				# quay Registry에서 Image를 검색	
```

- Search Image at localhost
```bash
$ docker images
```

- Pull
```bash
# [중요] 하나의 공인 IP에 대해 6시간동안 100건?으로 제한 vs. 로그인 후에는 150건? at hub.docker.com
$ docker pull nginx				                                 # Default(at hub.docker.com, Tag 생략 시 lastest)
$ docker pull quay.io/uvelyster/nginx				               # at quay.io/uvelyster/nginx(Domain/Owner/Repository, Tag 생략 시 lastest)
$ docker pull plutomsw/cicd_guestbook:20240107064001_24    # at hub.docker.com/plutomsw/cicd_guestbook:20240107064001_24(Domain/Owner/Repository:Tag)
```

- Run and Example
```bash
$ docker run quay.io/uvelyster/nginx                       # Forground(stdout + stderr) Mode
$ docker run -d quay.io/uvelyster/nginx                    # Background(stdout is none) Mode
$ docker run -i quay.io/uvelyster/nginx                    # Interactive(stdin + stdout + stderr) Mode
$ curl 172.17.0.2                                          # [중요] Default Container Network=172.17.0.0/16(Default: Container Host에서만 접속 가능)
$ docker run quay.io/uvelyster/nginx echo helloworld       # Command Parameter(echo helloworld)
$ docker run -d --name demoApp quay.io/uvelyster/nginx     # Background Mode(-d) + Alias Name(--name)
$ docker run -i -t --name demoApp quay.io/uvelyster/nginx  # Interactive(stdin + stdout + stderr) / TTY Mode(=-it) + Alias Name
$ docker ps
$ docker ps -a
$ docker inspect demoApp
$ docker cp demoApp:/usr/share/nginx/html/index.html .             # 컨테이너 파일을 로컬(.)로 복사
$ nano index.html
$ docker cp ./index.html demoApp:/usr/share/nginx/html/index.html  # 로컬 파일을 컨테이너 파일로 복사
$ docker exec -it demoApp /bin/bash				                         # [중요] 해당 컨테이너에 접근=exec addtional process(i: Interactive, t: TTY) after run(PID=1)
$ exit                                                             # 해당 컨테이너에서 나가기
```

- LifeCycle for Container
```bash
$ docker create [IMAGE]
$ docker start [IMAGE]                                                          # run = create + start
$ docker restart [NAME or CONTAINER ID%]
$ docker stop [NAME or CONTAINER ID%]                                           # SIGTERM(15)에 해당하는 안전 종료(참고: kill -l)
$ docker kill [NAME or CONTAINER ID%]                                           # SIGTERM(9)에 해당하는 강제 종료
$ docker logs -f [CONTAINER ID%]
$ docker inspect [NAME or CONTAINER ID%]
$ docker inspect -f '{{ .NetworkSettings.IPAddress }}' [NAME or CONTAINER ID%]  # IP 확인
$ docker rm [CONTAINER ID%]                                                     # 중지되어 있어야 삭제 가능
$ docker run --rm                                                               # 실행 후 즉시 삭제
$ docker rm -f $(docker container ls -a -q)                                     # 모든 컨테이너 삭제(-f: 강제 중지 후 삭제)
$ docker rmi [IMAGE]                                                            # 이미지 삭제(해당 컨테이너가 삭제되어야 이미지 삭제 가능, -f 시 강제 삭제)
```


- Network for Container
```bash
$ docker network ls                                # Default Network(Default:bridge=자체, host=호스트, none=없음) for Container
$ docker pull quay.io/uvelyster/busybox
# docker tag quay.io/uvelyster/busybox mybusybox   # quay.io/uvelyster/busybox를 mybusybox로 설정(tag)
$ docker images
$ docker run --rm mybusybox ip a                   # 실행 후 즉시 삭제(--rm), 이미지, IP 확인(ip a): 172.17.0.2 from 172.17.0.0 ~ 172.17.255.255
$ docker run --rm --network host mybusybox ip a    # 실행 후 즉시 삭제(--rm), 네트워크 선택(--network), 이미지(=docker.io/library/busybox:lastest), IP 확인(ip a)
$ docker run --rm --network none mybusybox ip a    # 실행 후 즉시 삭제(--rm), 네트워크 선택(--network), 이미지(=docker.io/library/busybox:lastest), IP 확인(ip a)
```
