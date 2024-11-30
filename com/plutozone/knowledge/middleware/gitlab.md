# com.plutozone.knowledge.middleware.GitLab


- Install Git Client
```bash
# Git Client 설치
$ sudo apt install git

# Git Client 버전 확인
$ git --version

# Git Client 정보 등록
$ git config --global user.name pluto
$ git config --global user.mail pluto@plutozone.com

# Git Glient 정보 확인
$ git config -l
$ cat ~/.gitconfig

# Gi Client 정보 삭제(또는 pico ~/.gitconfig)
$ git config --unset --global user.name
$ git config --unset --global user.mail

# Git Clone 및 확인
$ git clone URL
$ ls -al

# Git Pull at Repository Directory
$ git pull

# 접속된 계정 정보를 삭제
# 1. 특정 저장소(local)
git config --local --unset credential.helper  

# 2. 시스템의 특정 사용자(global)
git config --global --unset credential.helper 

# 3. 시스템의 모든 사용자와 모든 저장소(system)
git config --system --unset credential.helper

# 접속된 계정 정보를 저장(~/.git-credentials에 텍스트로 저장되므로 저장이 필요할 경우 Keychain 등을 통해 암호화를 권장)
git config --global credential.helper store
```

- Install GitLab Server
```bash
# [옵션] SSH Server 설치
$ sudo apt install -y curl openssh-server ca-certificates

# curl을 통해 Gitlab CE(Community Edition) 저장소 추가, 업데이트 및 설치
$ sudo curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
$ sudo apt update
$ sudo apt -y install gitlab-ce
# [참고] sudo EXTERNAL_URL="https://gitlab.plutozone.com" apt-get install gitlab-ce 명령을 통하여 URL을 지정하여 설치 가능
# [참고] SMTP 설정(참고: https://docs.gitlab.com/omnibus/settings/smtp.html)
# [참고] gitlab-ce를 gitlab-ee(Enterprise Edition)로 변경 가능
```

- Configuration and Maintenance of GitLab Server
```bash
# 설정 변경
$ sudo pico /etc/gitlab/gitlab.rb
...
# [참고] 도메인 또는 IP 변경 설정
external_url 'https://gitlab.plutozone.com'
...
# 설정 적용
$ sudo gitlab-ctl reconfigure

# Uninstall
$ sudo gitlab-ctl uninstall

# Restart
$ sudo gitlab-ctl restart

# 콘솔 접속 등
$ sudo gitlab-rails console
# Using the console find the the id of the ghost user and delete it
# 예) ghost 계정 정보 확인
> user = User.find_by(username: "ghost")
# [예제] 해당 계정의 ID로 계정 삭제
> User.delete(user.id)
# If the user is removed then output would be 1, if 0 then user is not removed
```
