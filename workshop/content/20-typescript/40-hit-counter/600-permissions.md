+++
title = "Granting permissions"
weight = 600
+++

## Allow Lambda to read/write our DynamoDB table

Lambda의 Role 에 테이블을 읽기고 쓰기기 위한 권한을 부여하겠습니다. 

`hitcounter.ts` 에서 다음과 같이 강조된 코드를 추가해주세요.

{{<highlight ts "hl_lines=32-33">}}
import * as cdk from '@aws-cdk/core';
import * as lambda from '@aws-cdk/aws-lambda';
import * as dynamodb from '@aws-cdk/aws-dynamodb';

export interface HitCounterProps {
  /** the function for which we want to count url hits **/
  downstream: lambda.Function;
}

export class HitCounter extends cdk.Construct {

  /** allows accessing the counter function */
  public readonly handler: lambda.Function;

  constructor(scope: cdk.Construct, id: string, props: HitCounterProps) {
    super(scope, id);

    const table = new dynamodb.Table(this, 'Hits', {
        partitionKey: { name: 'path', type: dynamodb.AttributeType.STRING }
    });
    
    this.handler = new lambda.Function(this, 'HitCounterHandler', {
      runtime: lambda.Runtime.NODEJS_10_X,
      handler: 'hitcounter.handler',
      code: lambda.Code.fromAsset('lambda'),
      environment: {
        DOWNSTREAM_FUNCTION_NAME: props.downstream.functionName,
        HITS_TABLE_NAME: table.tableName
      }
    });
    
    // grant the lambda role read/write permissions to our table
    table.grantReadWriteData(this.handler);
  }
}
{{</highlight>}}

## Deploy

저장하고 배포합니다.

```
cdk deploy
```

## Test again

배포가 끝났네요. 다시 테스트 해볼까요. (`curl` 또는 웹 브라우저를 사용합니다):

```
curl -i https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod/
```

또 에러인가요?

```
HTTP/2 502 Bad Gateway
...

{"message": "Internal server error"}
```

# 😢

5xx error 에러가 발생하네요. CloudWatch logs 로그를 다시 볼까요?

```json
{
    "errorMessage": "User: arn:aws:sts::585695036304:assumed-role/CdkWorkshopStack-HelloHitCounterHitCounterHandlerS-TU5M09L1UBID/CdkWorkshopStack-HelloHitCounterHitCounterHandlerD-144HVUNEWRWEO is not authorized to perform: lambda:InvokeFunction on resource: arn:aws:lambda:us-east-1:585695036304:function:CdkWorkshopStack-HelloHandler2E4FBA4D-149MVAO4969O7",
    "errorType": "AccessDeniedException",
    "stackTrace": [
        "Object.extractError (/var/runtime/node_modules/aws-sdk/lib/protocol/json.js:48:27)",
        "Request.extractError (/var/runtime/node_modules/aws-sdk/lib/protocol/rest_json.js:52:8)",
        "Request.callListeners (/var/runtime/node_modules/aws-sdk/lib/sequential_executor.js:105:20)",
        "Request.emit (/var/runtime/node_modules/aws-sdk/lib/sequential_executor.js:77:10)",
        "Request.emit (/var/runtime/node_modules/aws-sdk/lib/request.js:683:14)",
        "Request.transition (/var/runtime/node_modules/aws-sdk/lib/request.js:22:10)",
        "AcceptorStateMachine.runTo (/var/runtime/node_modules/aws-sdk/lib/state_machine.js:14:12)",
        "/var/runtime/node_modules/aws-sdk/lib/state_machine.js:26:10",
        "Request.<anonymous> (/var/runtime/node_modules/aws-sdk/lib/request.js:38:9)",
        "Request.<anonymous> (/var/runtime/node_modules/aws-sdk/lib/request.js:685:12)"
    ]
}
```

또 다른 Access Denied 메시지네요. 자세히 보면 ...

```
User: <VERY-LONG-STRING> is not authorized to perform: lambda:InvokeFunction on resource: <VERY-LONG-STRING>"
```

이번에는 우리의 hit counter가 데이터베이스에 쓰기를 할 수 있었던 것 같습니다. 이것은 [DynamoDB 콘솔](https://console.aws.amazon.com/dynamodb/home) 에서 확인할 수 있습니다.

![](./logs5.png)

그러나 다운스트림 Lambda 함수를 호출 할 수 있는 hit counter 권한도 부여해야 합니다.

## Grant invoke permissions

아래의 강조된 코드를 `lib/hitcounter.ts` 에 추가합니다.

{{<highlight ts "hl_lines=35-36">}}
import * as cdk from '@aws-cdk/core';
import * as lambda from '@aws-cdk/aws-lambda';
import * as dynamodb from '@aws-cdk/aws-dynamodb';

export interface HitCounterProps {
  /** the function for which we want to count url hits **/
  downstream: lambda.Function;
}

export class HitCounter extends cdk.Construct {

  /** allows accessing the counter function */
  public readonly handler: lambda.Function;

  constructor(scope: cdk.Construct, id: string, props: HitCounterProps) {
    super(scope, id);

    const table = new dynamodb.Table(this, 'Hits', {
        partitionKey: { name: 'path', type: dynamodb.AttributeType.STRING }
    });
    
    this.handler = new lambda.Function(this, 'HitCounterHandler', {
      runtime: lambda.Runtime.NODEJS_10_X,
      handler: 'hitcounter.handler',
      code: lambda.Code.fromAsset('lambda'),
      environment: {
        DOWNSTREAM_FUNCTION_NAME: props.downstream.functionName,
        HITS_TABLE_NAME: table.tableName
      }
    });
    
    // grant the lambda role read/write permissions to our table
    table.grantReadWriteData(this.handler);
    
    // grant the lambda role invoke permissions to the downstream function
    props.downstream.grantInvoke(this.handler);
  }
}
{{</highlight>}}

## Diff

`cdk diff` 명령을 사용해서 새롭게 추가한 코드가 무엇을 하는지 확인할 수 있습니다.

```
cdk diff
```

**Resource** 부분에서 IAM 구문이 Role에 추가된 것을 확인할 수 있습니다.

```
Resources
[~] AWS::IAM::Policy HelloHitCounter/HitCounterHandler/ServiceRole/DefaultPolicy HelloHitCounterHitCounterHandlerServiceRoleDefaultPolicy1487A60A
 └─ [~] PolicyDocument
     └─ [~] .Statement:
         └─ @@ -19,5 +19,15 @@
            [ ]         "Arn"
            [ ]       ]
            [ ]     }
            [+]   },
            [+]   {
            [+]     "Action": "lambda:InvokeFunction",
            [+]     "Effect": "Allow",
            [+]     "Resource": {
            [+]       "Fn::GetAtt": [
            [+]         "HelloHandler2E4FBA4D",
            [+]         "Arn"
            [+]       ]
            [+]     }
            [ ]   }
            [ ] ]
```

우리가 정확히 원했던 것이죠.

## Deploy

다시 한번 해볼까요.

```
cdk deploy
```

그리고 `curl` 또는 웹 브라우저로 엔드포인트에 접속합니다.

```
curl -i https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod/
```

다음과 같은 결과를 확인할 수 있습니다.

```
HTTP/2 200 OK
...

Hello, CDK! You've hit /
```

> 여전히 5xx 에러가 발생한다면, 잠깐 후에 다시한번 해보세요. API Gateway는 종종 새로운 배포본을 사용하기 위한 엔드포인트 전환에 약간 시간이 소요되기도 합니다.

# 😲
