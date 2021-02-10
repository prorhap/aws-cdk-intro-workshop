+++
title = "API Gateway"
weight = 300
+++

다음 단계는 우리의 Lambda 함수 앞에 API Gateway를 추가하는 것 입니다. API Gateway는 인터넷에 있는 사용자에게 HTTP 엔드포인트를 노출하고, [curl](https://curl.haxx.se/) 또는 웹 브라우저와 같은 HTTP 클라이언트를 사용해서 접속할 수 있게 합니다.

우리는, API의 루트에 마운트 된 [Lambda 프록시 통합](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-create-api-as-simple-proxy-for-lambda.html) 을 사용합니다. 즉, URL 경로에 대한 모든 요청은 Lambda 함수로 직접 프록시되고 함수의 응답이 사용자에게 다시 반환됩니다.

## Install the API Gateway construct library

```
npm install @aws-cdk/aws-apigateway
```

{{% notice info %}}

**Windows 사용자**: Windows에서는 백그라운드에서 실행중인 `npm run watch` 명령을 중지하고, `npm install`을 실행 한 다음 `npm run watch` 를 다시 시작해야 합니다. 그렇지 않으면 사용중인 파일에 대한 오류가 발생합니다.

{{% /notice %}}

## Add a LambdaRestApi construct to your stack

`lib/cdk-workshop-stack.ts` 로 돌아가서, API 엔드포인트를 정의하고 이것을 우리의 Lambda 함수에 엮습니다.

{{<highlight ts "hl_lines=3 16-19">}}
import * as cdk from '@aws-cdk/core';
import * as lambda from '@aws-cdk/aws-lambda';
import * as apigw from '@aws-cdk/aws-apigateway';

export class CdkWorkshopStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // defines an AWS Lambda resource
    const hello = new lambda.Function(this, 'HelloHandler', {
      runtime: lambda.Runtime.NODEJS_10_X,    // execution environment
      code: lambda.Code.fromAsset('lambda'),  // code loaded from "lambda" directory
      handler: 'hello.handler'                // file is "hello", function is "handler"
    });
    
    // defines an API Gateway REST API resource backed by our "hello" function.
    new apigw.LambdaRestApi(this, 'Endpoint', {
      handler: hello
    });

  }
}
{{</highlight>}}

이게 끝이에요! 이것이 AWS Lambda 함수에 대한 모든 요청을 프록시하는 API Gateway 를 정의하기 위해 필요한 모든 작업입니다.

## cdk diff

이제 이것을 배포하면 어떤 변화가 있는지 살펴볼까요.

```
cdk diff
```

결과는 다음과 같습니다.

```text
Stack CdkWorkshopStack
IAM Statement Changes
┌───┬───────────────────────────┬────────┬───────────────────────────┬───────────────────────────┬─────────────────────────────┐
│   │ Resource                  │ Effect │ Action                    │ Principal                 │ Condition                   │
├───┼───────────────────────────┼────────┼───────────────────────────┼───────────────────────────┼─────────────────────────────┤
│ + │ ${Endpoint/CloudWatchRole │ Allow  │ sts:AssumeRole            │ Service:apigateway.${AWS: │                             │
│   │ .Arn}                     │        │                           │ :URLSuffix}               │                             │
├───┼───────────────────────────┼────────┼───────────────────────────┼───────────────────────────┼─────────────────────────────┤
│ + │ ${HelloHandler.Arn}       │ Allow  │ lambda:InvokeFunction     │ Service:apigateway.amazon │ "ArnLike": {                │
│   │                           │        │                           │ aws.com                   │   "AWS:SourceArn": "arn:${A │
│   │                           │        │                           │                           │ WS::Partition}:execute-api: │
│   │                           │        │                           │                           │ ${AWS::Region}:${AWS::Accou │
│   │                           │        │                           │                           │ ntId}:${EndpointEEF1FD8F}/$ │
│   │                           │        │                           │                           │ {Endpoint/DeploymentStage.p │
│   │                           │        │                           │                           │ rod}/*/"                    │
│   │                           │        │                           │                           │ }                           │
│ + │ ${HelloHandler.Arn}       │ Allow  │ lambda:InvokeFunction     │ Service:apigateway.amazon │ "ArnLike": {                │
│   │                           │        │                           │ aws.com                   │   "AWS:SourceArn": "arn:${A │
│   │                           │        │                           │                           │ WS::Partition}:execute-api: │
│   │                           │        │                           │                           │ ${AWS::Region}:${AWS::Accou │
│   │                           │        │                           │                           │ ntId}:${EndpointEEF1FD8F}/t │
│   │                           │        │                           │                           │ est-invoke-stage/*/"        │
│   │                           │        │                           │                           │ }                           │
│ + │ ${HelloHandler.Arn}       │ Allow  │ lambda:InvokeFunction     │ Service:apigateway.amazon │ "ArnLike": {                │
│   │                           │        │                           │ aws.com                   │   "AWS:SourceArn": "arn:${A │
│   │                           │        │                           │                           │ WS::Partition}:execute-api: │
│   │                           │        │                           │                           │ ${AWS::Region}:${AWS::Accou │
│   │                           │        │                           │                           │ ntId}:${EndpointEEF1FD8F}/$ │
│   │                           │        │                           │                           │ {Endpoint/DeploymentStage.p │
│   │                           │        │                           │                           │ rod}/*/{proxy+}"            │
│   │                           │        │                           │                           │ }                           │
│ + │ ${HelloHandler.Arn}       │ Allow  │ lambda:InvokeFunction     │ Service:apigateway.amazon │ "ArnLike": {                │
│   │                           │        │                           │ aws.com                   │   "AWS:SourceArn": "arn:${A │
│   │                           │        │                           │                           │ WS::Partition}:execute-api: │
│   │                           │        │                           │                           │ ${AWS::Region}:${AWS::Accou │
│   │                           │        │                           │                           │ ntId}:${EndpointEEF1FD8F}/t │
│   │                           │        │                           │                           │ est-invoke-stage/*/{proxy+} │
│   │                           │        │                           │                           │ "                           │
│   │                           │        │                           │                           │ }                           │
└───┴───────────────────────────┴────────┴───────────────────────────┴───────────────────────────┴─────────────────────────────┘
IAM Policy Changes
┌───┬────────────────────────────┬─────────────────────────────────────────────────────────────────────────────────────────┐
│   │ Resource                   │ Managed Policy ARN                                                                      │
├───┼────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────────────┤
│ + │ ${Endpoint/CloudWatchRole} │ arn:${AWS::Partition}:iam::aws:policy/service-role/AmazonAPIGatewayPushToCloudWatchLogs │
└───┴────────────────────────────┴─────────────────────────────────────────────────────────────────────────────────────────┘
(NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)

Resources
[+] AWS::ApiGateway::RestApi Endpoint EndpointEEF1FD8F
[+] AWS::ApiGateway::Deployment Endpoint/Deployment EndpointDeployment318525DA37c0e38727e25b4317827bf43e918fbf
[+] AWS::ApiGateway::Stage Endpoint/DeploymentStage.prod EndpointDeploymentStageprodB78BEEA0
[+] AWS::IAM::Role Endpoint/CloudWatchRole EndpointCloudWatchRoleC3C64E0F
[+] AWS::ApiGateway::Account Endpoint/Account EndpointAccountB8304247
[+] AWS::ApiGateway::Resource Endpoint/Default/{proxy+} Endpointproxy39E2174E
[+] AWS::Lambda::Permission Endpoint/Default/{proxy+}/ANY/ApiPermission.CdkWorkshopStackEndpoint018E8349.ANY..{proxy+} EndpointproxyANYApiPermissionCdkWorkshopStackEndpoint018E8349ANYproxy747DCA52
[+] AWS::Lambda::Permission Endpoint/Default/{proxy+}/ANY/ApiPermission.Test.CdkWorkshopStackEndpoint018E8349.ANY..{proxy+} EndpointproxyANYApiPermissionTestCdkWorkshopStackEndpoint018E8349ANYproxy41939001
[+] AWS::ApiGateway::Method Endpoint/Default/{proxy+}/ANY EndpointproxyANYC09721C5
[+] AWS::Lambda::Permission Endpoint/Default/ANY/ApiPermission.CdkWorkshopStackEndpoint018E8349.ANY.. EndpointANYApiPermissionCdkWorkshopStackEndpoint018E8349ANYE84BEB04
[+] AWS::Lambda::Permission Endpoint/Default/ANY/ApiPermission.Test.CdkWorkshopStackEndpoint018E8349.ANY.. EndpointANYApiPermissionTestCdkWorkshopStackEndpoint018E8349ANYB6CC1B64
[+] AWS::ApiGateway::Method Endpoint/Default/ANY EndpointANY485C938B

Outputs
[+] Output Endpoint/Endpoint Endpoint8024A810: {"Value":{"Fn::Join":["",["https://",{"Ref":"EndpointEEF1FD8F"},".execute-api.",{"Ref":"AWS::Region"},".",{"Ref":"AWS::URLSuffix"},"/",{"Ref":"EndpointDeploymentStageprodB78BEEA0"},"/"]]}}
```

멋지네요. 이 코드 한 줄은 12 개의 새로운 리소스를 스택에 추가했습니다.

## cdk deploy

좋아요, 이제 배포할 준비가 됐죠?

```
cdk deploy
```

## Stack outputs

배포가 완료되면 다음와 같은 내용이 표시됩니다.

```
CdkWorkshopStack.Endpoint8024A810 = https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod/
```

이것은 API Gateway 엔드포인트의 URL 에 대한 [스택 출력 (stack output)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html) 입니다.

## Testing your app

이제 `curl` 을 사용해서 위 엔드포인트에 접속해볼까요. URL을 복사해서 실행해봅시다. (위 예의 URL은 여러분들이 생성한 URL과 다릅니다)

{{% notice info %}}
만악 [curl](https://curl.haxx.se/)이 설치되어있지 않다면, 웹 브라우저에 URL을 입력해주세요.
{{% /notice %}}

```
curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod/
```

실행 결과는 다음과 같습니다.

```
Hello, CDK! You've hit /
```

다음과 같이 웹 브라우저를 사용할 수도 있어요.

![](./browser.png)

실행 결과가 위와 같다면, 여러분의 앱은 잘 동작한 것 입니다.

## What if it didn't work?

만약 API Gateway 에서 5xx 에러를 받았다면, 두 가지 문제 중 하나 일 가능성이 높습니다.

1. 함수에서 반환한 응답이 API Gateway가 예상하는 것과 다른 경우 입니다. 코드로 돌아가서 handler에서 `statusCode`,
   `body` and `header` 에 대한 값을 반환하는지 확인해 주세요. (참고: [Write handler runtime
   code](./200-lambda.html)).
2. 어떤 이유로 인해 함수가 실패할 수 있습니다. [이 페이지](../40-hit-counter/500-logs.html)의 Lambda 로그 확인 법을 참고해서 문제를 디버깅 해주세요.

---

수고하셨습니다! 이제 다음 장에서 재사용 가능한 construct를 작성해 봅시다.

