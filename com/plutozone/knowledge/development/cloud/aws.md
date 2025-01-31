# com.plutozone.knowledge.cloud.AWS(Amazon Web Service)


## Fundamental
- On-Premise vs. Cloud
- OpenStack for IaaS vs.OpenShift for PaaS by Red Hat


## Term, Login and Service
### Term
- EIP(Elastic IP=Public and Static IP, 최대 5개) vs. PIP(Public IP=Public and Dynamic IP)

### Login
- Sign in as the root user
- Sign in as an IAM user(비용 설정 및 사용량 확인 불가)

### Service
- ACM(Amazon Certificate Manager=HTTPS/SSL 인증서 관리)
	- 상용(com, co.kr 등)이 아닌 개인(store, shop 등) 도메인에 대한 SSL 인증서 발급 무료 지원
- ECS(Elastic Container Service) vs. EKS(Elastic Kubernetes Service)
- ELB(Elastic Load Balancing=L4)
- IAM(Identity and Access Management=Account)
- VPC(Virtual Private Cloud=Network, 최대 5개)
	- **Inside(=Private) for WAS + Outside(=Public) for WS vs. All Inside(=Private)**
	- CIDR(Classless Inter-Domain Routing) is not modified at AWS
	- **Private IP Band**
		- 10.0.0.0 ~ 10.255.255.255(10.0.0.0/8)
   		- 172.16.0.0 ~ 172.31.255.255(172.16.0.0/12)
		- 192.168.0.0 ~ 192.168.255.255(192.168.0.0/16)
	- 2A(롯데정보통신/현대정보기술 용인 마북리)/2C(LG U+ 평촌) AZ(Availability Zone) is difference from 2B(KT 목동)/2D(SKB 일산) at ap-northeast-2(아시아 태평양-서울)
- EC2(Elastic Computing Cloud=Host)
	- Type: Micro(M), Free Tier(T), ...
	- AMI(Amazon Machine Image): Amazon Linux, Ubuntu, Windows, ...
	- ELB(Elastic Load Balancer) Function vs. Auto Scaling by EC2(Amazon EC2 Auto Scaling, Not Support LB and HC)
		- Load Balancer
		- Health Check
		- Auto Scaling
	- ELB(Elastic Load Balancer) Option
		- Application Load Balancer(L7 + Containers, ...)
		- Network Load Balancer(L4, ...)
		- Classic Load Balancer(L7/4 + EC2 Instances, ...)
	- Storage at EC2
		- Instance Storage(예: C:\)
		- EBS(Elastic Block Storage, 예: D:\)
	- Create EC2 Instances
		- Make PEM for ec2-user at Amazon Linux 2
		- Install Nginx or Tomcat
- Route53(=DNS, 자체 또는 외부 DNS 관리)


## Step for Create Network and EC2 Instances
1. Make `VPC`(=전체 인프라 네트워크) at VPC
	- Select Region: ap-northeast-2(아시아 태평양-서울)
	- Name Tag: PLZ-PRD-VPC(PRD or STG or DEV)
	- IPv4 CIDR: 10.0.0.0/16(65,563)
	- 참고적으로 VPN 설정에서 DNS 호스트 이름를 활성화할 것
2. Make `Subnet`(=서비스별 네트워크) at VPC
 	- Select AZ: 2A and 2C(예: 장애 방지를 위해 Free Tier를 지원하는 2개의 Region에 Subnet을 생성)
	- **BST(Bastion)은 선택적으로 생성**
 	- Name Tag(IPv4 CIDR) for 2A
		- PLZ-PRD-VPC-2A-BST(10.0.0.0/24)
		- PLZ-PRD-VPC-2A-PUB(10.0.1.0/24)
		- PLZ-PRD-VPC-2A-PRI(10.0.64.0/24)
	- Name Tag(IPv4 CIDR) for 2C
		- PLZ-PRD-VPC-2C-BST(10.0.128.0/24)
		- PLZ-PRD-VPC-2C-PUB(10.0.129.0/24)
		- PLZ-PRD-VPC-2C-PRI(10.0.192.0/24)
3. Make `Routing Table`(AZ간의 통신을 위한 라우팅 테이블) at VPC
	- Name Tags: PLZ-PRD-RT-BST, PLZ-PRD-RT-PUB, PLZ-PRD-RT-PRI
	- Setting Subnet(PLZ-PRD-VPC-2A-BST, PLZ-PRD-VPC-2C-BST) for PLZ-PRD-RT-BST at `Subnet Connection`
 	- Setting Subnet(PLZ-PRD-VPC-2A-PUB, PLZ-PRD-VPC-2C-PUB) for PLZ-PRD-RT-PUB at `Subnet Connection`
 	- Setting Subnet(PLZ-PRD-VPC-2A-PRI, PLZ-PRD-VPC-2C-PRI) for PLZ-PRD-RT-PRI at `Subnet Connection`
4. Make `Internet Gateway` for Public Subnet(=Public Subnet을 위한 Outbound 네트워크) at VPC
	- Name Tag: PLZ-PRD-IGW
	- Setting PLZ-PRD-IGW for Internet Gateway at PLZ-PRD-VPC
5. Make `NAT Gateway` for Private Subnet(=Private Subnet을 위한 Outbound 네트워크, 비용 절감을 위해 2A에만 생성) at VPC
	- Name Tag: PLZ-PRD-NGW-2A(and 2C)
	- Select Subnet: PLZ-PRD-VPC-2A-PUB(and 2C-PUB)
	- Assign Elastic IP(참고: 최대 5개의 EIP에서 1개 사용됨) and Binding
6. Setting up Routing(Public/Private Subnet를 위한 Outbound 설정 등) at `Rouning Table` at VPC
	- Select PLZ-PRD-RT-BST and Setting PLZ-PRD-IGW at `Routing`(Insert Outbound for 0.0.0.0)
 	- Select PLZ-PRD-RT-PUB and Setting PLZ-PRD-IGW at `Routing`(Insert Outbound for 0.0.0.0)
	- Select PLZ-PRD-RT-PRI and Setting PLZ-PRD-NGW-2A at `Routing`(Insert Outbound for 0.0.0.0)
7. Make SG(`Security Group`) and Binding(Inbound 설정) at VPC or EC2
	- PLZ-PRD-SG-2A-BST(SSH)는 선택적으로 설정
	- PLZ-PRD-SG-2C-BST(SSH)는 선택적으로 설정
	- PLZ-PRD-SG-2A-PUB(SSH, HTTP, HTTPS)
	- PLZ-PRD-SG-2C-PUB(SSH, HTTP, HTTPS)
	- PLZ-PRD-SG-2A-PRI(SSH 등)는 선택적으로 설정
	- PLZ-PRD-SG-2C-PRI(SSH 등)는 선택적으로 설정
8. Make EC2 for WS(예: PLZ-PRD-EC2-2A-PUB-NGINX-001), WAS(예: PLZ-PRD-EC2-2A-PRI-TOMCAT-001), DB 등 at EC2
   	- Configure Hostname
	- Select Amazon Linux2nd(t2.micro)
	- Create or Select Key Pair
 	- Select Public IP Enable 
 	- Select Subnet & SG(Security Grooup)
  	- Configure Private IP
9. Make LB at EC2
	- **Make CLB(Classic LB) for only HTTP**
		- Make SG(PLZ-PRD-SG-CLB-WS: HTTP)
	 	- Select Classic LB(PLZ-PRD-CLB-WS) and Setting up ...
   		- Setting up Instances
	- **Make ALB(Application LB) for HTTPS**
		- Make Target Group(PLZ-PRD-ALB-WS-TG) and Setting up Instances
		- Make SG(PLZ-PRD-SG-ALB-WS: HTTP, HTTPS)
	 	- Make Classic(참고: Instance vs. IP/Base on Container) LB(PLZ-PRD-ALB-WS) and Setting up ...
10. Make A Record for Domain Service at Route53
	- Setting up ...


## Step for Auto Scaling
1. `Create Network and EC2 Instances`
2. Make Launch Template(PLZ-PRD-AS-TP) for Auto Scaling
3. Make Auto Scaling Group(PLZ-PRD-AS)


## Step for Remove
1. Remove EC2
2. Remove Security Group(Delete Configuration, First!)
3. Remove Target Group and Load Balance
4. Remove Routing Table(Delete Configuration, First!)
5. Remove Internet and NAT Gateway
6. Remove Subnet
7. Remove VPC


## 기타
- [주의] **t2.micro는 720시간 동안만 무료로 사용 가능**
- [주의] **EIP(Elastic IP)를 EC2 등에 할당하지 않을 경우 별도 추가 과금 발생**
- [주의] **생성한 VPCs, EC2s, SCs, LBs 등의 삭제는 역순으로**
- [권장] **현재 일반적으로 Bastion Server(관제용), NAT Gateway(패치용), EKS Management Server(관리용)만을 Public Zone에 배치**
- [권장] **ssh -i keyPair.pem id@localhost at Bastion Server**
- [참고] 새로운 SG(Security Group) 생성 후 Inbound 설정할 경우 Outbound는 Any로 자동 설정됨
- [참고] Amazon Linux, Ubuntu의 기본 계정은 ec2-user, ubuntu
- [참고] EC2, NAT Gateway는 화면 상에서 즉시 삭제되지 않고 추후 삭제됨
