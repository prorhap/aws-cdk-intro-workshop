+++
title = "Test the hit counter"
weight = 700
+++

## Issue a few test requests

몇 가지 요청을 발생시키고 hit counter가 작동하는지 확인하겠습니다. 웹 브라우저를 사용해도됩니다.

```
curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod/
curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod/
curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod/hello
curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod/hello/world
curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod/hello/world
```

## Open DynamoDB console

1. [DynamoDB 콘솔](https://console.aws.amazon.com/dynamodb/home) 로 이동합니다.

2. 테이블을 만든 Region에 있는지 확인합니다.

3. 탐색 창에서 `Tables`를 선택하고 `CdkWorkdShopStack-HelloHitCounterHits`로 시작하는 테이블을 선택합니다.

4. 테이블을 열고 "Items" 를 선택합니다.

5. 각 경로에 대해 얼마나 많은 히트를 기록했는지 확인합니다.

    ![](./dynamo1.png)

6. 새 경로를 입력하고 테이블을 ''새로고침'' 합니다. '조회수'가 1인 새 항목이 표시되어야 합니다.

## Good job!

'HitCounter'의 멋진 점은 매우 유용하다는 것입니다. 기본적으로 누구나 API Gateway 프록시 백엔드 역할을하는 Lambda 함수에 "연결"할 수 있고 API에 hit을 기록합니다. 

히트 카운터는 간단한 Javascript 클래스이므로 npm 모듈로 패키징하여 Javascript 패키지 관리자 인 [npmjs.org](http://npmjs.org/)에 게시할 수 있습니다. 그런 다음 누구나 'npm 설치'하여 CDK 앱에 추가 할 수 있습니다.

-----

In the next chapter we __consume__ a construct library published to
npm, which enables us to view the contents of our hit counter table from any
browser.
