:octocat: [denlyou/TroubleShooting](https://github.com/denlyou/TroubleShooting)
> Trouble Shooting

## Java Server (Tomcat8.5)세팅기
- Oracle JDK 설치
- Tomcat8 설치 및 설정
  - 자동 시작하게 하기 (systemctl)
- mariaDB 연동
- nginx 연동 (reverse proxy)

#### case :
`새로운 프로젝트가 Java로 Server를 사용하기로 했다고 한다`

### 서버 환경 체크
- aws t2.micro
- utuntu 16.04 LTS

### Oracle JDK 설치
- ~~openjdk로 시작했다가 퍼포먼스에 교체 (openjdk는 java연습용, 영원히?!)~~
- repogitory 업데이트
```shell
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java9-installer
```
- JAVA_HOME 환경 변수 설정
  - `/etc/environment` 파일에 추가
```shell
JAVA_HOME="/usr/lib/jvm/java-9-oracle"
```
  - 편집후 반영
```shell
$ sudo source /etc/environment
$ export JAVA_HOME
```

### Tomcat8 설치 및 설정
> (apt 사용 x)

- [공식 홈페이지](http://tomcat.apache.org/) 다운로드 후 직접 세팅
- 다운로드
```shell
$ sudo wget http://apache.tt.co.kr/tomcat/tomcat-8/v8.5.13/bin/apache-tomcat-8.5.13.tar.gz
$ tar -xvf apache-tomcat-8.5.13.tar.gz
```
- 하위 폴더 퍼미션 설정
```shell
$ cd /tomcat_root/
$ sudo chmod -R g+r conf
$ sudo chmod g+x conf
$ sudo chown -R tomcat webapps/ work/ temp/ logs/
```
- service 등록 (`/etc/systemd/system/tomcat.service` 파일 생성)  

```shell
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/lib/jvm/java-9-oracle
Environment=CATALINA_PID=/tomcat_root/temp/tomcat.pid
Environment=CATALINA_HOME=/tomcat_root
Environment=CATALINA_BASE=/tomcat_root
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

ExecStart=/tomcat_root/bin/startup.sh
ExecStop=/tomcat_root/bin/shutdown.sh

User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```
- 설정 적용 및 자동 시작하게
```shell
$ sudo systemctl daemon-reload
$ sudo systemctl enable tomcat
```
- 포트 개방
```shell
$ sudo ufw allow 8080
```

### MariaDB 연동
- [JDBC driver (MariaDB Connector) 다운로드](https://mariadb.com/kb/en/mariadb/about-mariadb-connector-j/)
- Maven 추가
```xml
<dependency>
    <groupId>org.mariadb.jdbc</groupId>
    <artifactId>mariadb-java-client</artifactId>
    <version>xxx</version>
</dependency>
```
- 프로젝트의 `META-INF/context.xml`파일 생성
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Context docBase="hs" path="/hs" reloadable="true" source="org.eclipse.jst.jee.server:HelloServlet">
    <Resource name="jdbc/이름" auth="Container" type="javax.sql.DataSource"
                       maxTotal="100" maxIdle="30" maxWaitMillis="10000"
                       username="DB유저" password="패스워드" driverClassName="org.mariadb.jdbc.Driver"
                       url="jdbc:mariadb://서버주소:3306/디비명"/>
</Context>
```

### nginx에서 연동
>php와 같이 사용하기 위함이였음...

- `/etc/nginx/sites-available/default` 파일 수정
```shell
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /tomcat_root/webapps;

        index index.html index.htm;

        server_name _;

        # 정적 파일들
        location ~* \.(html|js|css|jpe?g|png|gif|ico|zip|pdf|mp3|mp4|avi|map|woff2|woff|ttf)$ {
                root /tomcat_root/webapps;
        }

        # 확장자가 php인 파일은 php에서 처리
        location ~ \.php$ {
                root /tomcat_root/webapps;

                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        }

        # 그외에는 모두 tomcat 으로 처리
        location / {
                root /tomcat_root/webapps;
                proxy_set_header X-Forwarded-Host $host;
                proxy_set_header X-Forwarded-Server $host;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
                proxy_set_header X-NginX-Proxy true;
                proxy_pass http://127.0.0.1:8080/;
                proxy_redirect off;
        }
}
```

---
##### 참고
- https://www.digitalocean.com/community/tutorials/how-to-install-apache-tomcat-8-on-ubuntu-16-04
