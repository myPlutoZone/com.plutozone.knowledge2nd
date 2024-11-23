# com.plutozone.knowledge.cloud.AWS(Amazon Web Service)


## Fundamental & Term
- On-Premise vs. Cloud
- OpenStack for IaaS vs.OpenShift for PaaS by Red Hat


## 계정 및 서비스 종류
### 계정
- 루트
- 일반(비용 설정 및 사용량 확인 불가)

### 서비스
- Route53(=DNS, 자체 또는 외부 DNS 관리 가능)
- ACM(Amazon Certificate Manager=HTTPS/SSL 인증서)
  - com, co.kr 등 상용이 아닌 store, shop 등 개인 도메인에 대한 무료 SSL 인증서 발급 가능
- IAM(Identity and Access Management=Account)
- ELB(Elastic Load Balancing=LB)
- VPC(Virtual Private Cloud=Network, 최대 5개)
  	- Inside(=Private) for WAS + Outside(=Public) for WS vs. All Inside(=Private)
	  - CIDR(Classless Inter-Domain Routing) is not modified at AWS
	  - Private IP Band
		  - 10.0.0.0 ~ 10.255.255.255(10.0.0.0/8)
		  - 172.16.0.0 ~ 172.31.255.255(172.16.0.0/12)
		  - 192.168.0.0 ~ 192.168.255.255(192.168.0.0/16)
   - 2A(롯데정보통신/현대정보기술 용인)/2C(LG U+ 평촌) AZ(Availability Zone) is difference from 2B(KT 목동)/2D(SKB 일산) at ap-northeast-2(아시아 태평양-서울)
- EC2(=Host)
  - 타입: Micro(M), Free Tier(T) 등
- EIP(Elastic IP=Public and Static IP, 최대 5개)
- ECS(Elastic Container Service)
- EKS(Elastic Kubernetes Service)


## Create Network at VPC for EC2 Instances


## 참고 사항
- [주의] EIP를 EC2 등에 할당하지 않을 경우 별도 추가 과금 발생
- [권장] EC2 vs. Container
