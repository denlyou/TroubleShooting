:octocat: [denlyou/TroubleShooting](https://github.com/denlyou/TroubleShooting)
> Trouble Shooting

## ie9 브라우저 호환성 맞추기 삽질기
- jquery ajax 통신 문제
  - 대안 : axios 라이브러리 사용
- 시간 계산 문제
  - momentjs 라이브러리 활용
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

> axios를 이용해 보는 것도 좋은 대안? (ie8까지 지원중) https://github.com/mzabriskie/axios

#### 시간 계산 문제
- timezone문제로 `2010-05-24T24:00:00+0900`와 같은 시간 사용했었는데 크롬에서는 문제 없던 것이 익스에서는 제대로 동작을 안함
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
- div로 selectbox만들기 => http://webclub.tistory.com/235
> 추천 라이브러리 => https://tympanus.net/Development/SelectInspiration/

- option 폰트 size (ie11이상은 css에서 font-size로 가능 그이하 jquery이용 )
```js
if(jQuery.browser.msie) $("select").css("font-size", "150%");
```

- 한개의 아이템 찾아 지우기 example (ie10이하에서 `객체.remove()` 이 먹히지 않음)
```js
var all_el_ul = document.getElementsByClassName('element_list')[0];
var div_list  = all_el_ul.getElementsByTagName("div");

for (i = 0; i < div_list.length; i += 1) {         
   div_list[i].parentNode.removeChild(div_list[i]);             
}
```
> 출처 http://stackoverflow.com/questions/20428877/javascript-remove-doesnt-work-in-ie
