# com.plutozone.knowledge.os.Ubuntu


- 호스트 네임 변경
```bash
$ sudo hostnamectl set-hostname plutozone
```

- cloud-init
```bash
# cloud-init를 비활성화(disabled) 또는 제거
# 방법 1) cloud-init를 비활성화(disabled)하는 파일을 생성하고 재부팅한다.
sudo touch /etc/cloud/cloud-init.disabled
sudo reboot

# 방법 2) cloud-init 패키지와 폴더를 삭제하고 재부팅한다.
sudo apt purge cloud-init -y
sudo rm -rf /etc/cloud && sudo rm -rf /var/lib/cloud/
sudo reboot
 sudo hostnamectl set-hostname plutozone
```

- 시간대(TimeZone) 설정
```bash
# 시간대를 서울(KST)로 변경
pluto@ubuntu:~$ sudo timedatectl set-timezone Asia/Seoul

# 변경된 시간대 확인
pluto@ubuntu:~$ date
```

- root 계정으로 전환
```bash
pluto@ubuntu:~$ sudo -i
root@ubutu:~# cd /
root@ubutu:/#
```

- 고정(Static) IP 설정 over Ubuntu 20.04.x
```bash
# 고정 IP 설정
pluto@ubuntu:~$ sudo nano /etc/netplan/*.yaml
network:
 ethernets:
  enp0s3:
   dhcp4: no
   addresses: [192.168.0.100/24]
   gateway4: 192.168.0.1
   nameservers:
    addresses: [168.126.63.1,8.8.8.8]
   routes:
   - to: 100.100.100.0/24
     via: 192.168.0.1
 version: 2

# 설정 적용
pluto@ubuntu:~$ sudo netplan apply

# 설정 확인
pluto@ubuntu:~$ netstat -rn
```