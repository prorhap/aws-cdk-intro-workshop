+++
title = "Cleanup sample"
weight = 100
+++

## Delete the sample code from your stack

`cdk init sample-app`에서 생성 한 프로젝트에는 SQS queue와 SNS topic이 포함됩니다. 우리는 앞으로 이것을 사용하지 않을 것이기 때문에 `CdkWorkshopStack` 생성자 안에서 해당 코드를 지웁니다.

 `lib/cdk-workshop-stack.ts`를 열어서 코드를 지웁니다. 그러면 다음과 같이 보일 것 입니다.

```ts
import * as cdk from '@aws-cdk/core';

export class CdkWorkshopStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // nothing here!
  }
}
```

## cdk diff

스택의 내용을 수정했기 때문에 Toolkit에 CDK 앱과 현재 배포된 것 사이의 차이점을 보여달라고 요청할 수 있습니다. 이것은 `cdk deploy`를 실행하면 어떤 일이 일어날 지 확인하는 안전한 방법이며 언제나 좋은 사례라고 할 수 있습니다.

```
cdk diff
```

다음과 같은 내용을 확인할 수 있습니다.

```
Stack CdkWorkshopStack
IAM Statement Changes
┌───┬─────────────────────────────────┬────────┬─────────────────┬───────────────────────────┬──────────────────────────────────────────────────┐
│   │ Resource                        │ Effect │ Action          │ Principal                 │ Condition                                        │
├───┼─────────────────────────────────┼────────┼─────────────────┼───────────────────────────┼──────────────────────────────────────────────────┤
│ - │ ${CdkWorkshopQueue50D9D426.Arn} │ Allow  │ sqs:SendMessage │ Service:sns.amazonaws.com │ "ArnEquals": {                                   │
│   │                                 │        │                 │                           │   "aws:SourceArn": "${CdkWorkshopTopicD368A42F}" │
│   │                                 │        │                 │                           │ }                                                │
└───┴─────────────────────────────────┴────────┴─────────────────┴───────────────────────────┴──────────────────────────────────────────────────┘
(NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)

Resources
[-] AWS::SQS::Queue CdkWorkshopQueue50D9D426 destroy
[-] AWS::SQS::QueuePolicy CdkWorkshopQueuePolicyAF2494A5 destroy
[-] AWS::SNS::Topic CdkWorkshopTopicD368A42F destroy
[-] AWS::SNS::Subscription CdkWorkshopTopicCdkWorkshopQueueSubscription88D211C7 destroy
```

예상했듯이 우리의 모든 리소스가 제거될 것 입니다.

## cdk deploy

`cdk deploy` 를 실행하고 다음 섹션으로 이동합시다. (실행한 뒤에 기다릴 필요 없어요)

```
cdk deploy
```


