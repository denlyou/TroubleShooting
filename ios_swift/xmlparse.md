:octocat: [denlyou/TroubleShooting](https://github.com/denlyou/TroubleShooting)
> Trouble Shooting

## 정부 공공데이터 xml 파싱 삽질기
- 공공데이터 홈페이지 API 발급시 주의사항
  - restAPI 호출 query string과 urlencoding 주의
- NSXMLParserDelegate를 사용해도 구현은 가능하나 어려움..
- 외부 라이브러리 사용 예시 (Alamofire, SWXMLHash)

#### case :
`iOS(swift) 스터디에서 버스도착알람앱을 만들면서 격은 공공데이터 xml파싱 하면서 격은삽질`

>##### 개발 환경 체크
>- swift3 / ios8(target) / xcode 8.1(8T61a)

#### 공공데이터 홈페이지 API 발급시 주의사항
> https://www.data.go.kr/main.do
- 가입 및 개발자 신청은 바로 가능
- API키는 신청 즉시 나오지만 바로 사용 가능하지 않을 수도 있음
  - 여기는 중계 홈페이지지 실제로는 각 기관 데이터이고, 발급된API는 동기하는데 시간이 걸림

#### API와 통신부분 구현
- restAPI는 http get 방식 (파라미터를 url query string으로 요청)
 ~~제공되는 연동 API 문서는 갖다버려!~~
- urlencoding 주의
```swift
let keyword = "검색어"
let endcodingKeyword = keyword.addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed)!
let url = "http://openapi.gbis.go.kr/ws/rest/busstationservice?serviceKey=\(serviceKey)&keyword=\(endcodingKeyword)"
```
- [통신은 그냥 Alamofire 라이브러리 사용하기로 함](https://github.com/Alamofire/Alamofire)


#### MXL 파싱 부분
>- 별다른 라이브러리 없이도 NSXMLParserDelegate 프로토콜을 구현하면 가능함
>  - 심지어 url로 인스턴스 생성 파라미터를 집어넣으면 알아서 통신도 함
>  - http://rhammer.tistory.com/131
- [SWXMLHash 라이브러리 사용하기로 함](https://github.com/drmohundro/SWXMLHash)
  - xml을 그나마 접근하기 쉬운 형태로 만들어줌!

#### 샘플 코드

```swift
import Alamofire
import SWXMLHash
// ... 생략 ...
let serviceKey = "발급받은서비스키"
public func iKeywordSearch(_ keyword: String){
    let endcodingKeyword = keyword.addingPercentEncoding( withAllowedCharacters: .urlQueryAllowed)!
    let url = "http://openapi.gbis.go.kr/ws/rest/busstationservice?serviceKey=\(serviceKey)&keyword=\(endcodingKeyword)"
    // 공공데이터 API중 버스 정류소 검색 API 사용의 예

    Alamofire.request(url).responseString { response in
        let dataString = response.result.value!
        print(dataString)

        let xml = SWXMLHash.config{ config in
            config.shouldProcessLazily = true
        }.parse(dataString)
        // 한개 요소 접근 print example
        print((xml["response"]["msgHeader"]["resultMessage"].element?.text)!)
        // 반복 요소 접근 print example
        for elem in xml["response"]["msgBody"]["busStationList"].all {
            print( elem["stationName"].element!.text! )
            print( elem["stationId"].element!.text! )
        }
    }
}
```
