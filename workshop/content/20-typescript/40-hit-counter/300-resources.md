+++
title = "Define resources"
weight = 300
+++

## Add resources to the hit counter construct

이제 HitCounter constuct에서 AWS Lambda 함수와 DynamoDB 테이블을 정의하겠습니다.

평소와 같이, 먼저 DynamoDB construct 라이브러리를 설치해야합니다 (이미 Lambda 라이브러리가 설치되어 있음).:

```
npm install @aws-cdk/aws-dynamodb
```

{{% notice info %}}

**Windows 사용자**: Windows에서는 백그라운드에서 실행중인 `npm run watch` 명령을 중지하고, `npm install`을 실행 한 다음 `npm run watch` 를 다시 시작해야 합니다. 그렇지 않으면 사용중인 파일에 대한 오류가 발생합니다.

{{% /notice %}}

이제 `lib/hitcounter.ts` 로 돌아가서 다음과 같이 코드를 추가합니다.

{{<highlight ts "hl_lines=3 12-13 18-30">}}
import * as cdk from '@aws-cdk/core';
import * as lambda from '@aws-cdk/aws-lambda';
import * as dynamodb from '@aws-cdk/aws-dynamodb';

export interface HitCounterProps {
  /** the function for which we want to count url hits **/
  downstream: lambda.IFunction;
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
  }
}
{{</highlight>}}

## What did we do here?

이제 이 코드는 이해하기 쉬울 것입니다

 * 파티션 키로 `path`를 사용하여 DynamoDB 테이블을 정의했습니다.
 * `lambda/hitcounter.handler` 에 바인딩 된 Lambda 함수를 정의했습니다.
 * Lambda의 환경변수를 우리 리소스의 `functionName` 와 `tableName`에 **연결**했습니다.



