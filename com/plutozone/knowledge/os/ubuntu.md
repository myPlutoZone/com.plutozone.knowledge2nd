# com.plutozone.knowledge.Ubuntu
- Static IP
```bash
$ sudo nano /etc/netplan/*.yaml
network:
 ethernets:
  enp0s3:
   dhcp4: no
   addresses: [192.168.0.178/24]
   gateway4: 192.168.0.1
   nameservers:
    addresses: [192.168.0.101,168.126.63.1,8.8.8.8]
   routes:
   - to: 100.100.100.0/24
     via: 192.168.0.1
 version: 2

# 설정 정보 적용
$ sudo netplan apply

# 네트워크 설정 확인
$ netstat -rn
```

- TimeZone
```bash
# 시간대를 서울(KST)로 변경
$ sudo timedatectl set-timezone Asia/Seoul

# 변경된 시간대 확인
$ date
```