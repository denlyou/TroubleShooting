:octocat: [denlyou/TroubleShooting](https://github.com/denlyou/TroubleShooting)
> Trouble Shooting

## nodejs에서 sub domain별 다른 express 프로젝트를 연결하기
- mysql 모듈 연동
  - connection pool로 설정
  - golbal하게 사용하는 방법
- post로 보낸 요청의 param들은 어디로 들어오지?
-  Can't set headers after they are sent. 문제

#### case :
`nodejs의 가장 기초적인 공부를 끝내고 게시판 프로젝트를 시작하며 겪었던 삽질들..`

#### 들어가기 전에) 가장 효율적인 express의 best MVC Pettern code는?
[Best practices for Express app structure](https://www.terlici.com/2014/08/25/best-practices-express-structure.html)

> - generator로 생성된 app은 routes폴더에서 controller 시작하는데
> - controller를 파일로 나누지 않고 routes 안의 파일에서 다 처리하는게 일반적인가?

#### 들어가기 전에) View Engine은 pug(jade)가 좋은지?

> - 일단, 문법이 생소한 것이 가장 큰 진입의 장벽
> - 근데 `html로 변환이 되면 그 자체가 서버에서 작업의 부담을 주는 것이 아닌가?`란 의문
- 처음이니 그냥 ejs로 하기로 결정

#### mysql 모듈 연동
> [mysql module](https://github.com/mysqljs/mysql)

- [서버니까 connection pool로 설정 방법?](https://github.com/mysqljs/mysql#pooling-connections)
- [express에서 golbal하게 사용하는 최고의 code pattern은?](http://stackoverflow.com/questions/26552544/global-objects-with-node-js-express)

#### post로 보낸 요청의 param들은 어디로 들어오지?
> [Request 객체 Ref](http://expressjs.com/ko/4x/api.html#req)

- 결론은 `req.body`객체로 온다는 것을 알게 됨
- Request 객체의 사용법 자체를 너무 몰라 전체 레퍼런스를 훝어볼 필요가 있었음

##### Trouble) Error: Can't set headers after they are sent.
- [response의 header를 두번 설정하면 생기는 문제](http://stackoverflow.com/questions/7042340/error-cant-set-headers-after-they-are-sent-to-the-client)
- nodemon으로 돌렸는데도 이 문제로 죽으면 재실행이 안됨 :scream:
