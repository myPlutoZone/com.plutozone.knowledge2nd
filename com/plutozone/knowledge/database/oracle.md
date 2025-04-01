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
# Oracle에서는 Redhat 계열에서 사용하는 RPM 파일만을 제공하므로 Debian(Ubuntu 등) 계열에서 사용하는 DEB 파일로 변환하여 설치 및 설정을 진행해야 한다.
# [권장] Database와 Oracle를 관리할 별도 그룹(예: dba) 및 계정(예: oracle, sudo 권한 포함)으로 진행한다.
# 압축 파일 해제
$ unzip oracle-xe-11.2.0-1.0.x86_64.rpm.zip

# [필수] DEB로 변환하기 위해 alien 설치한다.
# [옵션] libaio1(Linux 커널 AIOAsynchronous I/O 엑세스) 및 unixodbc(Open Database Connectivity)는 설치 이후에 사용될 것으로 예상되므로 설치 제외한다.
$ sudo apt install -y alien
$ sudo apt install -y alien libaio1 unixodbc

# RPM 파일을 DEB 파일로 변환
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

# [옵션] Oracle XE는 /bin/awk를 사용하지만 Ubuntu에서는 /usr/bin/awk에 설치되기 때문에 심볼릭 링크 /bin/awk가 존재할 경우 제외한다.
$ sudo ln -s /usr/bin/awk /bin/awk

# Oracle XE의 리스너(Listener)가 사용할 lock 파일 생성한다.
# /var/lock/subsys 폴더가 존재할 경우 listener 파일만 생성
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
```bash
# Oracle XE 리스너 상태(status) 확인
$ lsnrctl status

# Oracle XE 접속(ID=system)
$ sqlplus system
```

#### 에러 발생 시
##### ORA-000845:MEMORY_TARGET 에러가 발생하게 되는 경우
메모리의 설정이 잘못되거나 사이즈가 부족한 경우이며 이런 경우 메모리 설정을 위해서 다음 과정을 진행한다.
```bash
# Oracle XE를 중지하고 현재 설정된 shared memeory 삭제한다.
$ sudo rm -rf /dev/shm

# 새로운 SHM을 생성 후 mount
$ sudo mkdir /dev/shm
$ sudo mount -t tmpfs shmfs -o -size=4096m /dev/shm

# SHM 설정을 데몬에 등록 및 로드하기 위해 아래 내용을 파일로 생성하고 오라클을 재시작한다.
$ sudo vi /etc/rc2.d/S01shm_load
#!/bin/sh
case "$1" in
start) mkdir /var/lock/subsys 2>/dev/null
	touch /var/lock/subsys/listener
	rm /dev/shm 2>/dev/null
	mkdir /dev/shm 2>/dev/null
	mount -t tmpfs shmfs -o size=4096m /dev/shm ;;
*) echo error
exit 1 ;;
esac
$ sudo chmod 755 /etc/rc2.d/S01shm_load
```

#### 설정
##### Oracle Application Express 원격 접속 허용 및 포트번호
```bash
# Oracle XE APEX 원격 접속 허용(FALSE) 시 하기와 같이 설정한다.
$ sqlplus system
…
SQL> EXEC DBMS_XDB.SETLISTENERLOCALACCESS(FALSE);
SQL> exit

# Oracle XE APEX 포트번호 확인, 변경 또는 서비스 종료(포트번호=0)
$ sqlplus system
…
SQL> SELECT DBMS_XDB.GETHTTPPORT() FROM DUAL;
SQL> EXEC DBMS_XDB.SETHTTPPORT(포트번호);
SQL> exit
# Oracle XE APEX 접속 경로는 http://localhost:port/apex
# Default Workspace 및 User는 INTERNAL, ADMIN이며 암호는 SYS/SYSTEM과 동일하다.
```

#### Application을 위한 Database 생성 및 설정
단, XE 버전은 신규 SID를 생성할 수 없으므로 기존 SID(XE)를 이용하여 하기 작업을 진행한다.
##### [예제] Table Space 생성
```sql
$ sqlplus system
…
-- Table Space 생성 전에 기존 Table Space 정보를 확인한다.
SQL> SELECT * FROM DBA_DATA_FILES;
…
SQL> CREATE TABLESPACE "OpenMalls"  datafile '/u01/app/oracle/oradata/XE/OpenMalls.dbf'  SIZE 100M reuse autoextend ON;
SQL> CREATE TABLESPACE "Backoffice" datafile '/u01/app/oracle/oradata/XE/Backoffice.dbf' SIZE 100M reuse autoextend ON;
SQL> CREATE TABLESPACE "Membership" datafile '/u01/app/oracle/oradata/XE/Membership.dbf' SIZE 100M reuse autoextend ON;
SQL> CREATE TABLESPACE "Message"    datafile '/u01/app/oracle/oradata/XE/Message.dbf'    SIZE 100M reuse autoextend ON;
```
##### [예제] User 생성
```sql
SQL> CREATE USER openmalls  IDENTIFIED BY password DEFAULT TABLESPACE "OpenMalls"  TEMPORARY TABLESPACE temp;
SQL> CREATE USER backoffice IDENTIFIED BY password DEFAULT TABLESPACE "Backoffice" TEMPORARY TABLESPACE temp;
SQL> CREATE USER membership IDENTIFIED BY password DEFAULT TABLESPACE "Membership" TEMPORARY TABLESPACE temp;
SQL> CREATE USER message    IDENTIFIED BY password DEFAULT TABLESPACE "Message"    TEMPORARY TABLESPACE temp;
-- User(membership) 삭제
SQL> DROP USER membership CASCADE;

```
##### [예제] User에게 사용자 권한 부여
```sql
-- 권한 부여는 GRANT, 취소는 REVOKE
SQL> GRANT connect, resource, dba to plutozone;
SQL> GRANT connect, resource to openmalls;
SQL> GRANT connect, resource to backoffice;
SQL> GRANT CREATE SESSION, SELECT ANY TABLE to membership;
SQL> GRANT CREATE SESSION, SELECT ANY TABLE to message;
```

#### 설치 제거
```bash
# Oracle XE 서비스 정지
$ sudo service oracle-xe stop

# Oracle XE 패키지 삭제
$ sudo dpkg --purge oracle-xe

# Oracle XE 폴더 삭제
$ sudo rm -rf /u01/app

# Oracle XE 데몬 삭제 및 갱신
$ sudo rm /etc/default/oracle-xe
$ sudo update-rc.d oracle-xe remove

# Oracle XE 설정 파일 삭제
$ sudo rm /sbin/chkconfig
$ sudo rm /etc/rc2.d/s01shm_load
$ sudo rm /etc/sysctl.d/60-oracle.conf

# 필요 시 Oracle XE 그룹 및 계정 삭제
$ sudo userdel -r oracle
$ sudo delgroup dba
```


## Export and Import
```cmd
C:\>exp userid=ID/PASSWD file=D:\FILE_NAME.dmp		# Export
C:\>imp userid=ID/PASSWD file=D:\FILE_NAME.dmp		# Import
```