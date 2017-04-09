:octocat: [denlyou/TroubleShooting](https://github.com/denlyou/TroubleShooting)
# Trouble Shooting

## Category

- **[2017/04/08]** [Java Server (Tomcat8.5)세팅기](server/tomcat8setting.md)
>- Oracle JDK 설치
>- Tomcat8 설치 및 설정
>  - 자동 시작하게 하기 (systemctl)
>- mariaDB 연동
>- nginx 연동 (reverse proxy)

- **[2017/03/25]** [iOS 개발 : 정부 공공데이터 xml 파싱 삽질기](ios_swift/xmlparse.md)
>- 공공데이터 홈페이지 API 발급시 주의사항
>  - restAPI 호출 query string과 urlencoding 주의
>- NSXMLParserDelegate를 사용해도 구현은 가능하나 어려움..
>- 외부 라이브러리 사용 예시 (Alamofire, SWXMLHash)

- **[2017/01/14]** [ie9 브라우저 호환성 맞추기 삽질기](frontend/ie9.md)
>- jquery ajax 통신 문제
>  - 대안 : axios 라이브러리
>- 시간 계산 문제
>  - momentjs 라이브러리 활용
>- selectbox 커스텀

- **[2017/01/10]** [크롬 푸시 공부(삽질기) = Firebase 웹FCM](firebase/web2fcm.md)
> - ~~삽질 메모~~
> - [영문 FCM 가이드](https://firebase.google.com/docs/cloud-messaging/js/client)
> - 요점 정리
>  - firebase 프로젝트 만들고 firebase SDK를 가져와(CDN) 설정 및 초기화하기
>  - push 권한 확인하기 (브라우저 지원여부 체크)
>  - 웹 유저를 식별할 FCM Token 만들기
>  - fcm을 받았을 경우에 처리하기 (firebase-messaging-sw.js)
>  - [Messaging Reference](https://firebase.google.com/docs/reference/js/firebase.messaging.Messaging)

- **[2016/12/30]** [express(nodejs)로 게시판 만들기 첫걸음](nodejs/firstboard.md)
> - mysql 모듈 연동
>  - connection pool로 설정
>  - golbal하게 사용하는 방법
> - post로 보낸 요청의 param들은 어디로 들어오지?
> -  Can't set headers after they are sent. 문제


- **[2016/12/23]** [nodejs에서 sub domain별 다른 express 프로젝트를 연결하기](server/reverseproxy.md)
> - 하나의 http(80포트)로 request를 분배
> - nodejs-nginx reverse proxy
