+++
title = "Hit counter handler"
weight = 100
+++

## Hit counter Lambda handler

이제 hit counter에 대한 Lambda 핸들러 코드를 작성해 보겠습니다..

 `lambda/hitcounter.js` 파일을 생성합니다.

```js
const { DynamoDB, Lambda } = require('aws-sdk');

exports.handler = async function(event) {
  console.log("request:", JSON.stringify(event, undefined, 2));

  // create AWS SDK clients
  const dynamo = new DynamoDB();
  const lambda = new Lambda();

  // update dynamo entry for "path" with hits++
  await dynamo.updateItem({
    TableName: process.env.HITS_TABLE_NAME,
    Key: { path: { S: event.path } },
    UpdateExpression: 'ADD hits :incr',
    ExpressionAttributeValues: { ':incr': { N: '1' } }
  }).promise();

  // call downstream function and capture response
  const resp = await lambda.invoke({
    FunctionName: process.env.DOWNSTREAM_FUNCTION_NAME,
    Payload: JSON.stringify(event)
  }).promise();

  console.log('downstream response:', JSON.stringify(resp, undefined, 2));

  // return response back to upstream caller
  return JSON.parse(resp.Payload);
};
```

## Discovering resources at runtime

코드를 보면, 두 개의 환경 변수를 사용한 다는 것을 알 수 있습니다.:

 * `HITS_TABLE_NAME` 는 저장할 때 사용할 DynamoDB 테이블의 이름 입니다.
 * `DOWNSTREAM_FUNCTION_NAME` 는 호출하게 될 Lambda 함수 이름 입니다.

테이블의 실제 이름과 다운스트림 함수는 앱을 배포 할 때 결정되므로, construct 코드에서 이 값을 연결해야합니다. 다음 섹션에서 이 작업을 할 것 입니다.

