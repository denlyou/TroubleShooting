:octocat: [denlyou/TroubleShooting](https://github.com/denlyou/TroubleShooting)
> Trouble Shooting

## 크롬 푸시 공부(삽질기) = Firebase 웹FCM
- ~~삽질 메모~~
- [영문 FCM 가이드](https://firebase.google.com/docs/cloud-messaging/js/client)
- 요점 정리
  - firebase 프로젝트 만들고 firebase SDK를 가져와(CDN) 설정 및 초기화하기
  - push 권한 확인하기 (브라우저 지원여부 체크)
  - 웹 유저를 식별할 FCM Token 만들기
  - fcm을 받았을 경우에 처리하기 (firebase-messaging-sw.js)
  - [Messaging Reference](https://firebase.google.com/docs/reference/js/firebase.messaging.Messaging)

#### case :
`firebase가 web에 fcm을 지원하기 시작한 이후로 첫삽질기.`
> 주의할 것은 현재는 chrome과 firefox정도에서만 정상 동작함. (이유는 아마도 웹브라우저에서 동작하는 ServiceWorker때문일까?!)

#### 삽질 메모
> ##### 한글 파이어베이스 도움말 (x)
> - https://firebase.google.com/docs/cloud-messaging/chrome/client
> - 이거 따라하면 별로 실용적이지 않음 (2017년 1월 시점)
> - 구글 코드랩 (웹 클라이언트 사용)
>   - https://developers.google.com/web/fundamentals/getting-started/codelabs/push-notifications/
>   - 크롬 푸시 원리를 이해하기에는 도움이 됨 (fcm에서 내부적 구현되어 있음)
>
> ##### 서비스 워커
> - 웹페이지/서버 외에 웹브라우저에서 동작하는 일꾼(?!)
> - 대표적인 역할 **백그라운드 데이터 동기화**, **서버의 PUSH 수신** 정도를 수행
>   - 크롬 푸시(push) 알림(notification)가 동작하는데 가장 큰 원동력!
> - 좋은 참고 자료 :  https://developers.google.com/web/fundamentals/getting-started/primers/service-workers?hl=ko
> - `JavaScript를 사용해 구현!`

#### (아직 번역이 다 되지 않아 링크도 안걸린 ~~진짜~~) 영문 FCM 가이드
- https://firebase.google.com/docs/cloud-messaging/js/client
- 더 좋은 건 firebase 공식 youtube 채널인 firecasts에 동영상 가이드 참고
  - https://www.youtube.com/watch?v=BsCBCudx58g

#### 요점 정리
- firebase 프로젝트 만들고 firebase SDK를 가져와(CDN) 설정 및 초기화하기

```html
<script src="https://www.gstatic.com/firebasejs/3.3.0/firebase.js"></script>
<script>
// Initialize Firebase
var config = {
  apiKey: "<API_KEY>",
  authDomain: "<PROJECT_ID>.firebaseapp.com",
  databaseURL: "https://<DATABASE_NAME>.firebaseio.com",
  storageBucket: "<BUCKET>.appspot.com",
  messagingSenderId: "<SENDER_ID>"
};
firebase.initializeApp(config);

const messaging = firebase.messaging();
</script>
```

- push 권한 확인하기 (브라우저 지원여부 체크)
  - 주의 : 사용자가 거부했을 수도 있으니 분기 처리

```js
messaging.requestPermission().then(function() {
  console.log('PUSH 권한 있음.');
}).catch(function(err) {
  console.log('PUSH 권한 없음 :', err);
});
```

- 웹 유저를 식별할 FCM Token 만들기 (브라우져마다 다름)

```js
messaging.getToken().then(function(token){
  console.log(token);
  // TODO 서버로 보내서 저장해야 구분해 메세지를 보내지요
}).catch(function(err) {
  console.log('에러 :', err);
});
```

- fcm을 받았을 경우에 처리하기 (브라우저에 사이트가 열려 있을때)

```js
// 메세지를 받았을떄
messaging.onMessage(function(payload){
  console.log('메세지 수신 : ', payload);
  // TODO
})
```

- fcm을 받았을 경우에 처리하기 (사이트가 열려 있지 않을때)
  - Service Worker를 사용하는데 **root 폴더** 에 `firebase-messaging-sw.js`이름으로 고정

```js
// firebase-messaging-sw.js 샘플
importScripts('https://www.gstatic.com/firebasejs/3.5.2/firebase-app.js');
importScripts('https://www.gstatic.com/firebasejs/3.5.2/firebase-messaging.js');

firebase.initializeApp({
  'messagingSenderId': '884363925942'
});

const messaging = firebase.messaging();
messaging.setBackgroundMessaingHandler(function(payload) {
  const title = "안녕! 웹FCM!";
  const options = {
    data : payload.data.status
  };
  return self.registration.showNotification(title, options); // promise 리턴
});
```

- [Messaging Reference](https://firebase.google.com/docs/reference/js/firebase.messaging.Messaging)

---

#### :warning: Critical Trouble!!
`실서비스에서는 HTTPS를 적용한 곳에서만 사용 가능함`
- 무료 https 지원
  - [Lets' Encrypt](https://letsencrypt.org/) (3개월 마다 갱싱)
  - [StartSSL](https://www.startssl.com/) (1년마다 갱신 / 10개 제한)

> 코드짜는 것보다 서버에 ssl 적용하는 시간이 더 오래 걸림...
/play nyan
