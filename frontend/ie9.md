:octocat: [denlyou/TroubleShooting](https://github.com/denlyou/TroubleShooting)
> Trouble Shooting

## ie9 브라우저 호환성 맞추기
- jquery ajax 통신 문제
  - axios 대안
- 시간 계산 문제
  - momentjs
- selectbox 커스텀

#### case :
`외주로 퍼블리싱 작업을 하는데 ie9까지 맞추기로 함. 브라우저마다 달라서 발생하는 문제 정리`

#### jquery ajax 통신 문제
- ie9 에서 `$.post()`가 먹히질 않는다... `$.ajax()`에 type을 post로 해서 전달해야 함
```js
$.ajax({ type: "POST", dataType: "json",
  url: "/search_ajax_jobs.php",
  data: { 'jobs': _jobs },
  success: function(data){
    // TODO
  }
});
```

> - axios를 이용해 보는 것도 좋은 대안? (ie8까지 지원중) https://github.com/mzabriskie/axios

#### 시간 계산 문제
- timezone문제로 `2010-05-24T24:00:00+0900`와 같은 시간 사용하는을 크롬에서는 잘 변환하는데 익스에서는 제대로 동작을 안함
- moemntjs라는 좋은 라이브러리를 알게 됨 (http://momentjs.com/)
```js
moment("2010-10-20 4:30", "YYYY-MM-DD HH:mm");  // date 포멧과 시간 설정
var chdate = "2017-01-14T09:00:00+0900");
var ckdate = new Date( moment(chdate).unix()*1000 ); // unix 타임은 sec로 반환되서 1000을 계상해주어야 합
var _now = new Date();
if(ckdate.getTime() < _now.getTime()){
  alert('현재보다 과거 시간으로 설정할 수 없습니다.');
  return false;
};
```


#### selectbox 커스텀
- css로 변형 하는 방법이 있으나 그냥 기본 form 모양을 제거하고, 바깥에 div박스를 둘러싸서 배경 이미지 처리를 추천!
