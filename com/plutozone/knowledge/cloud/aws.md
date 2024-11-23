# com.plutozone.knowledge.cloud.AWS(Amazon Web Service)


## Fundamental & Term
- On-Premise vs. Cloud
- OpenStack for IaaS vs.OpenShift for PaaS by Red Hat


## 계정 및 서비스 종류
### 계정
- 루트
- 일반(비용 설정 및 사용량 확인 불가)

### 서비스
- Route53(=DNS, 자체 또는 외부 DNS 관리)
- ACM(Amazon Certificate Manager=HTTPS/SSL 인증서 관리)
	- 상용(com, co.kr 등)이 아닌 개인(store, shop 등) 도메인에 대한 무료 SSL 인증서 발급 지원
- IAM(Identity and Access Management=Account)
- ELB(Elastic Load Balancing=LB)
- VPC(Virtual Private Cloud=Network, 최대 5개)
	- Inside(=Private) for WAS + Outside(=Public) for WS vs. All Inside(=Private)
	- CIDR(Classless Inter-Domain Routing) is not modified at AWS
	- Private IP Band
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
- EIP(Elastic IP=Public and Static IP, 최대 5개)
- ECS(Elastic Container Service)
- EKS(Elastic Kubernetes Service)


## Step for Create Network and EC2 Instances at VPC
1. Make VPC(=전체 인프라 네트워크)
	- Select Region: ap-northeast-2(아시아 태평양-서울)
	- Name Tag: PLZ-PRD-VPC(PRD or STG or DEV)
	- IPv4 CIDR: 10.255.0.0/16(65,563)
	- 참고적으로 VPN 설정에서 DNS 호스트 이름를 활성화할 것
2. Make Subnet(=서비스별 네트워크)
	- Select AZ: 2A and 2C(예: 장애 방지를 위해 Free Tier를 지원하는 2개의 Region에 Subnet을 생성)
	- 2A
		- `PLZ-PRD-VPC-2A-BASTION`는 선택적으로 생성
		- Name Tag(IPv4 CIDR): PLZ-PRD-VPC-2A-PUB(10.255.0.0/24)
		- Name Tag(IPv4 CIDR): PLZ-PRD-VPC-2A-PRI(10.255.32.0/24)
	- 2C
		- Name Tag(IPv4 CIDR): PLZ-PRD-VPC-2C-PUB(10.255.128.0/24)
		- Name Tag(IPv4 CIDR): PLZ-PRD-VPC-2C-PRI(10.255.160.0/24)
3. Make `Routing Table`(AZ간의 통신을 위한 라우팅 테이블)
	- Name Tag: PLZ-PRD-RT-PUB
	- Name Tag: PLZ-PRD-RT-PRI
	- Select Routing Table at Subnet
4. Make `Internet Gateway for Public Subnet`(=Public Subnet을 위한 Outbound 네트워크)
	- Name Tag: PLZ-PRD-IGW
	- Select PLZ-PRD-IGW for Internet Gateway at PLZ-PRD-VPC
5. Make `NAT Gateway for Private Subnet`(=Private Subnet을 위한 Outbound 네트워크, 비용 절감을 위해 2A에만 생성)
	- Name Tag: PLZ-PRD-NGW-2A(and 2C)
	- Select Subnet: PLZ-PRD-VPC-2A-PUB(and 2C-PUB)
	- Assign Elastic IP(참고: 최대 5개의 EIP에서 1개 사용됨) and Binding
	- Select PLZ-PRD-NGW-2A for NAT Gateway at PLZ-PRD-RT-PRI
6. `Setting up Routing and Subnets`(Public/Private Subnet를 위한 Outbound 설정)
	- Select Rouning Table at VPC and Setting up Routing and Subnets
		- PLZ-PRD-RT-PUB
			- Setting PLZ-PRD-IGW for Routing
			- Setting PLZ-PRD-VPC-2A-PUB, PLZ-PRD-VPC-2C-PUB for Subnets
		- PLZ-PRD-RT-PRI
			- Setting PLZ-PRD-NGW-2A for Routing
			- Setting PLZ-PRD-VPC-2A-PRI, PLZ-PRD-VPC-2C-PRI for Subnets
7. Make SG(Security Group) and Binding(Inbound 설정)
	- For Subnet
		- `PLZ-PRD-SG-2A-BASTION`는 선택적으로 생성
		- PLZ-PRD-SG-2A-PUB(SSH, HTTP, HTTPS)
  		- PLZ-PRD-SG-2C-PUB(SSH, HTTP, HTTPS)
		- PLZ-PRD-SG-2A-PRI(SSH)
		- PLZ-PRD-SG-2C-PRI(SSH)
	- For ELB and Auto Scaling
		- PLZ-PRD-SG-LB(HTTP, HTTPS)
		- PLZ-PRD-SG-AS(HTTP, HTTPS)


## Step for Auto Scaling
1. Create VPCs
	- Make VPC
	- Make Subnet
	- Make Security Group
	- Make Internet Gateway and Select Public Subnet
	- Make NAT Gateway and Select Private Subnet
2. Create EC2
3. Create Target Group(PLZ-PRD-LB-TG) for ELB
4. Create ELB(PLZ-PRD-LB)
5. Create Launch Template(PLZ-PRD-AS-TP) for Auto Scaling
6. Create Auto Scaling Group(PLZ-PRD-AS)


## 참고 사항
- [주의] EIP를 EC2 등에 할당하지 않을 경우 별도 추가 과금 발생
- [권장] EC2 vs. Container
- [참고] 새로운 SG(Security Group) 생성 후 Inbound 설정할 경우 Outbound는 Any로 자동 설정됨
