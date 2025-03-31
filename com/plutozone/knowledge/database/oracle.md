# com.plutozone.knowledge.database.Oracle


## Contents
- Installation
- Export and Import


## Installation
본 설치는 Ubuntu 20.04.3와 Oracle Database Express Edition (EX) Release 11.2.0.2.0(11gR2)를 기준으로 한다.

### Oracle Database Express Edition (EX) Release 11.2.0
무료(2023년 2월 기준)이지만 상업적 목적으로도 사용 가능하고 아래와 같은 제한 사항이 있으며 이후 버전에서는 사용 가능한 저장 공간, 메모리, CPU가 증가하였다.
- Database 저장 공간 사용 제한: 11GB
- Memory 사용 제한: 1GB
- CPU 사용 제한: 1개(Single CPU)

#### Download
설치 파일을 https://www.oracle.com/database/technologies/xe-prior-release-downloads.html 또는 오라클 사이트에서 다운로드 한다.

#### RPM 파일을 DEB로 변환(alien) 그리고 설치 및 설정
```bash
# Oracle에서는 Redhat 계열에서 사용하는 RPM 파일만을 제공하므로 Debian 또는 Ubuntu 계열에서 사용하는 DEB 파일로 변환하여 설치 및 설정을 진행해야 한다.
# [권장] Database와 Oracle를 관리할 별도 그룹(예: dba) 및 계정(예: oracle, sudo 권한 포함)으로 진행한다.
# 압축 파일 해제
$ unzip oracle-xe-11.2.0-1.0.x86_64.rpm.zip

# [필수] DEB로 변환하기 위해 alien 설치한다.
# [옵션] libaio1(Linux 커널 AIOAsynchronous I/O 엑세스) 및 unixodbc(Open Database Connectivity)는 설치 이후에 사용될 것으로 예상되므로 설치 제외한다.
$ sudo apt install -y alien
$ sudo apt install -y alien libaio1 unixodbc

# DEB로 변환
$ sudo alien --scripts -d oracle*

# RPM 패키지가 설치 시 필요한 /sbin/chkconfig 파일을 생성한다.
$ sudo nano /sbin/chkconfig
#!/bin/bash
# Oracle 11g XE installer chkconfig hack for Ubuntu
file=/etc/init.d/oracle-xe
if [[ ! `tail -n1 $file | grep INIT` ]]; then
	echo >> $file
	echo '### BEGIN INIT INFO' >> $file
	echo '# Provides: OracleXE' >> $file
        echo '# Required-Start: $remote_fs $syslog' >> $file
        echo '# Required-Stop: $remote_fs $syslog' >> $file
        echo '# Default-Start: 2 3 4 5' >> $file
        echo '# Default-Stop: 0 1 6' >> $file
        echo '# Short-Description: Oracle 11g Express Edition' >> $file
        echo '### END INIT INFO' >> $file
fi
update-rc.d oracle-xe defaults 80 01
# EOF

# 실행 권한 부여
$ sudo chmod 755 /sbin/chkconfig

# Kernal 파라미터 설정
$ sudo nano /etc/sysctl.d/60-oracle.conf
# Oracle 11g XE kernel parameters
fs.file-max=6815744
net.ipv4.ip_local_port_range=9000 65000
kernel.sem=250 32000 100 128
kernel.shmmax=536870912

# Kernal 파리미터 로드(Load)
$ sudo service procps start

# Oracle XE는 /bin/awk를 사용하지만 Ubuntu에서는 /usr/bin/awk에 설치되기 때문에 심볼릭 링크
 /bin/awk가 존재할 경우 제외한다.
$ sudo ln -s /usr/bin/awk /bin/awk

# Oracle XE의 리스너(Listener)가 사용할 lock 파일 생성한다.
# /var/lock/subsys가 존재할 경우 제외
$ sudo mkdir /var/lock/subsys
$ sudo touch /var/lock/subsys/listener

# Oracle XE 설치 시작
$ sudo dpkg --install oracle*.deb

# Oracle XE 설정(서비스 포트와 비밀번호 등)
$ sudo /etc/init.d/oracle-xe configure
Oracle Database 11g Express Edition Configuration
-------------------------------------------------
This will configure on-boot properties of Oracle Database 11g Express
Edition.  The following questions will determine whether the database should
be starting upon system boot, the ports it will use, and the passwords that
will be used for database accounts.  Press <Enter> to accept the defaults.
Control-C will abort.

Specify the HTTP port that will be used for Oracle Application Express [8080]

Specify a port that will be used for the database listener [1521]

Specify a password to be used for database accounts.  Note that the same
password will be used for SYS and SYSTEM.  Oracle recommends the use of
different passwords for each database account.  This can be done after
initial configuration:
Confirm the password: 비밀번호

Do you want Oracle Database 11g Express Edition to be started on boot (y/n) [y]:y

Starting Oracle Net Listener...Done
Configuring database...
Starting Oracle Database 11g Express Edition instance...Done
Installation completed successfully.

# Oracle XE 환경 변수 설정(예: 설치 경로가 /u01일 경우)
# [권장] .bashrc도 가능하지만 .profile에 설정한다.
$ nano ~/.profile
…
export ORACLE_HOME=/u01/app/oracle/product/11.2.0/xe
export ORACLE_SID=XE
export NLS_LANG=`$ORACLE_HOME/bin/nls_lang.sh`
export ORACLE_BASE=/u01/app/oracle
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH
export PATH=$ORACLE_HOME/bin:$PATH
…

# Oracle XE 환경 변수 즉시 적용
$ source ~/.profile
```
#### 설치 확인

#### 에러 발생 시
##### ORA-000845:MEMORY_TARGET 에러가 발생하게 되는 경우

#### 설정
##### Oracle Application Express 원격 접속 허용 및 포트번호

#### Application을 위한 Database 생성 및 설정
##### [예제] Table Space 생성
##### [예제] User 생성
##### [예제] User에게 사용자 권한 부여
##### [예제] User가 다른 User에게 테이블 권한 여부

#### 설치 제거


## Export and Import
```cmd
C:\>exp userid=ID/PASSWD file=D:\FILE_NAME.dmp		# Export
C:\>imp userid=ID/PASSWD file=D:\FILE_NAME.dmp		# Import
```