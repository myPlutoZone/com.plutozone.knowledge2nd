# com.plutozone.knowledge.os.Ubuntu


- ROOT 계정으로 강제 전환
```bash
pluto@ubuntu:~$ sudo -i
root@ubutu:~# cd /
root@ubutu:/#
```

- 고정 IP(Static IP) 설정
```bash
# 고정 IP 설정
pluto@ubuntu:~$ sudo nano /etc/netplan/*.yaml
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

# 설정 적용
pluto@ubuntu:~$ sudo netplan apply

# 설정 확인
pluto@ubuntu:~$ netstat -rn
```

- 시간대(TimeZone) 설정
```bash
# 시간대를 서울(KST)로 변경
pluto@ubuntu:~$ sudo timedatectl set-timezone Asia/Seoul

# 변경된 시간대 확인
pluto@ubuntu:~$ date
```
