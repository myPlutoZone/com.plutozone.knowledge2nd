# com.plutozone.knowledge.middleware.Tomcat


## Recommand
- Web Server(예: Nginx)는 공용 서비스이므로 공용 계정(예: nginx:nginx는 Nginx 서비스 관리 권한 등) 및 개별 계정(예: backoffice:nginx는 리소스 관리 권한만) 생성
- Application Server(예: Tomcat)는 개별 서비스이므로 개별 계정(예: backoffice:tomcat, Tomcat 서비스 + 리소스 관리 권한만) 생성
- Each Domain User(계정: backoffice, 그룹: tomcat)
```bash
$ sudo groupadd tomcat
$ sudo useradd -g tomcat backoffice
$ sudo passwd backoffice
$ sudo mkdir /home/backoffice
$ sudo chown backoffice:tomcat backoffice
$ sudo usermod -s /bin/bash backoffice
```


## Installation and Configuration
- Install and Run(계정: backoffice, 그룹: tomcat)
```bash
$ tar zxvf apache-tomcat-9.0.16.tar.gz
$ cd ~/apache-tomcat-9.0.16/bin
$ chmod 755 startup.sh		// 필요 시 변경
$ chmod 755 shutdown.sh		// 필요 시 변경
$ ./start.sh
```

- catalina.out per Daily
```bash
$ pico catalina.sh
...
if [ -z "$CATALINA_OUT" ] ; then
    # CATALINA_OUT="$CATALINA_BASE"/logs/catalina.out   // 주석 처리
    CATALINA_OUT=/dev/null                              // null 처리
fi
...
```

- Automatic startup
```bash
$ sudo pico /etc/init.d/tomcat_backoffice
#!/bin/bash

### BEGIN INIT INFO
# Provides: tomcat
# Required-Start: $network
# Required-Stop: $network
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: Start/Stop Tomcat server
### END INIT INFO

PATH=/sbin:/bin:/usr/sbin:/usr/bin
export JAVA_HOME=/usr/local/jdk1.8.0_201
export JRE_HOME=/usr/local/jdk1.8.0_201/jre
export CLASSPATH=.:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin

start() {
	#sh /home/backoffice/apache-tomcat-9.0.16/bin/startup.sh                                  // Rus as root:root
	su backoffice -c "bash -c '/home/backoffice/apache-tomcat-9.0.16/bin/startup.sh'"         // Run as backoffice:tomcat
}

stop() {
	#sh /home/backoffice/apache-tomcat-9.0.16/bin/shutdown.sh                                 // Run as root:root
	su backoffice -c "bash -c '/home/backoffice/apache-tomcat-9.0.16/bin/shutdown.sh'"        // Run as backoffice:tomcat
}

case $1 in
start|stop) $1;;
restart) stop; start;;
*) echo "Run as $0 "; exit 1;;
esac
exit 0
$ cd /etc/init.d
$ sudo chmod 755 tomcat_backoffice
$ sudo update-rc.d tomcat_backoffice defaults        // 자동 실행 등록
$ sudo update-rc.d -f tomcat_backoffice remove       // 자동 실행 삭제
$ service tomcat_backoffice [start|stop]             // 실행 또는 중지
$ sudo reboot
```

- Restart
```bash
$ pico restart.sh
...
#!/bin/bash

#export LANG=ko_KR.UTF-8

echo "Shutdown Tomcat now by shutdown.sh"
bash /home/backoffice/apache-tomcat-9.0.16/bin/shutdown.sh

sleep 10

if [ -z "`ps -eaf | grep java | grep /home/backoffice/apache-tomcat-9.0.16/bin`" ]; then
	echo "[SUCCESS] Tomcat was terminated successfully."
else
	ps -eaf | grep java | grep /home/backoffice/apache-tomcat-9.0.16/bin | awk '{print $2}' |
	while read PID
		do
			echo "[WARNNING] Killing $PID..."
			kill -9 $PID
			echo "[INFORMATION] Tomcat was killed manually."
			done
fi

echo ""
echo "Startup Tomcat now by startup.sh"
bash /home/backoffice/apache-tomcat-9.0.16/bin/startup.sh
if [ -z "`ps -eaf | grep java | grep /home/backoffice/apache-tomcat-9.0.16/bin`" ]; then
	echo "[FAILURE] Tomcat was not started."
else
	echo "[SUCCESS] Tomcat was started successfully."
fi
...
```

- Multi Domain(계정: backoffice,  그룹: tomcat)
```bash
$ pico ~/apache-tomcat-9.0.16/conf/server.xml
...
# Server의 Shutdown 포트 지정
<Server port="8005" shutdown="SHUTDOWN">
    ...
    # 일반적으로 1개의 Service(name="Catalina")
    <Service name="Catalina">
        ...
        # 요청 및 응답 HTTP 인터페이스
        # [참고] 필요 시 Selinux 등록: # semanage  port -a -t http_port_t -p tcp 8080
        <Connector port="8080" protocol="HTTP/1.1", ..., redirectPort="8443" URIEncoding="UTF-8">
            ...
            # protocol="HTTP/1.1"           // 프로토콜
            # port="8080"                   // 서비스 포트
            # URIEncoding="UTF-8"           // [추가] URI 인코딩
            # connectionTimeout="3000"      // [권장] 3초(기본: 20초)
            # maxThreads="500"              // [옵션] 동시 접속자수(기본: 200)
            # acceptCount="10"              // [옵션] 유휴 thread가 없을때 대기 하는 큐의 숫자(기본: 100)
            # maxSpareThreads="50"          // [옵션] Max Thread Pool Size(기본: 50)
            # minSpareThreads="25"          // [옵션] Min Thread Pool Size(시작 시)
            # enableLookups="false"         // [옵션] 리포트 IP를 네임형태로 변형하는 옵션(기본: false)
            # compression="on"              // [옵션] 압축 전송
            # disableUploadTimeout="true"   // [옵션] 업로드 시 타임 아웃
            # maxHttpHeaderSize="8192"	    // [옵션] 헤드 사이즈가 커질 때(기본: 4K)
            # keepAliveTimeout="-1"         // [옵션] Keepalive off
            # maxKeepAliveRequests="-1"     // [옵션] Unlimited
        </Connector>
        ...
        # 요청 및 응답 AJP(Apache Jserv Protocal) 인터페이스, 불필요할 경우 삭제 가능
        <Connector port="8009" protocol="AJP/1.3", ..., redirectPort="8443" URIEncoding="UTF-8">
            ...
        </Connector>
        ...
        # Engine은 Host(일반적으로 1개)에 전달(default는 디폴트 Host name으로 지정)
        # Realm, Valve를 통하여 DB 연결, Single Sing On, Access Log 등 부가 기능 사용
        <Engine name="Catalina" default="localhost">
            ...
            # 클러스트링 사용 시(web.xml에 <distributable/> 추가 필요, port: 4000 ~ 4100)
            <Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster" ...>
            ...
            </Cluster>
                ...
            <Realm> ... </Realm>
            ...
            <Valve> ... </Valve>
            ...
            <Logger> ... </Logger>
            ...
            # "localhost"를 해당 도메인(d-admin.plutozone.co.kr)으로 수정
            # 자동 배포가 되지 않을 경우: unzip project.war -d $BASE
            <Host name="localhost" appBase="webapps", ...>
                ...
                # [선택] Host 내의 Web Application들 추가
                <Context path="" docBase="C:\...", ...>
                    ...
                    <Realm> ... </Realm>
                    ...
                    <Valve> ... </Valve>
                    ...
                    <Logger> ... </Logger>
                    ...
                </Context>
            </Host>
        </Engine>
    </Service>
</Server>
```


## Performance
- 동시 접속자(maxthreads) 설정
```bash
$ pico %TOMCAT%/conf/server.xml
 # connectionTimeout        : 클라이언트와 서버간 I/O가 설정한 시간동안 발생하지 않을 경우 타임아웃을 발생(Default: 5000)
 # MinSpareThreads          : Tomcat 실행시 생성되는 초기 쓰레드 사이즈
 # maxthreads               : 서버가 허용할수 있는 최대 요청(쓰레드) 수이며 실제 Active User 수를 의미(Default: 150)
 # acceptCount              : 쓰레드가 full 상태일 경우 요청을 대기하는 queue의 사이즈
 # maxConnection            : 최대 커넥션 수
 # maxKeepAliveRequests     : 종료되지 않고 동시에 대기할 수 있는 TCP 연결 수(Default: 1=비활성화)
 ...
<Connector port="28080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" maxThreads="600" />
...
<Connector port="8010"  protocol="AJP/1.3"  redirectPort="8443" maxThreads="600"/>
...
$ %TOMCAT%/bin/shutdown.sh
$ %TOMCAT%/bin/startup.sh
```


## Error Message
- Parameters: Invalid chunk ignored
```
8-1. 에러 메시지
11-Mar-2020 21:19:39.489 INFO [http-apr-8082-exec-2] org.apache.tomcat.util.http.Parameters.processParameters Invalid chunk starting at byte [6] and ending at byte [263] with a value of [=8de5cb7cd3662398dc632662cd8636face557d6a802b00c15357816d797bab4d34d60469e2f72d3c18551b90fbdf39041c8ca4c544d80f2ff165e72f6e8fa05f0a9c5c229b596eef802e71338a95ad43bb4df577664c991aeadfdace3d4c91057b0fee732cc2d65ec097b2a32ab46c063ba268e0c1c05236f8c5c03ee1c1d171] ignored
Note: further occurrences of Parameter errors will be logged at DEBUG level.

8-1. 방안(출처: http://kokiller2.blogspot.com/2010/03/parameters-invalid-chunk-ignored.html)
톰캣 '경고: Parameters: Invalid chunk ignored' 에러 해결 법

8-1. 증상
2010. 3. 4 오전 8:37:41 org.apache.tomcat.util.http.Parameters processParameters
경고: Parameters: Invalid chunk '' ignored.

8-1. 원인
Tomcat 에서 URL 주소에 '&&' 또는 '&=' 등이 들어가 있을 경우 발생되는 메시지이다.

8-1. 해결
톰캣설치경로/conf/logging.properties 파일에서 org.apache.tomcat.util.http.Parameters.level = SEVERE 를 추가하고 tomcat을 재시작 한다.
※ 주의: SERVER(X)와 SEVERE(O)를 혼돈하지 말것
위의 방법으로 해결되지 않을 경우
$JAVA_HOME/jre/lib/logging.properties 파일에 위의 내용을 추가한 후 tomcat을 재시작 한다.
```
