# com.plutozone.knowledge.database.Oracle


## Contents
- Installation
- Export and Import


## Installation
본 설치는 Ubuntu 20.04.5와 Oracle Database Express Edition (EX) Release 11.2.0.2.0(11gR2)를 기준으로 한다.

### Oracle Database Express Edition (EX) Release 11.2.0.2.0
무료(2023년 2월 기준)이지만 상업적 목적으로도 사용 가능하고 아래와 같은 제한 사항이 있으며 이후 버전에서는 사용 가능한 저장 공간, 메모리, CPU가 증가하였다.
- Database 저장 공간 사용 제한: 11GB
- Memory 사용 제한: 1GB
- CPU 사용 제한: 1개(Single CPU)

#### Download
https://www.oracle.com/database/technologies/xe-prior-release-downloads.html 또는 오라클 사이트에서 다운로드 한다.

#### 2.1.2	RPM 파일을 DEB로 변환(alien) 그리고 설치 및 설정
```bash
```


## Export and Import
```cmd
C:\>exp userid=ID/PASSWD file=D:\FILE_NAME.dmp		# Export
C:\>imp userid=ID/PASSWD file=D:\FILE_NAME.dmp		# Import
```