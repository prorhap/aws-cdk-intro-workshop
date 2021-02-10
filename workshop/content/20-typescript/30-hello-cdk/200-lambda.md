+++
title = "Hello Lambda"
weight = 200
+++

## Lambda handler code

우리는 AWS Lambda 핸들러 코드를 정의하며 시작합니다.

1. 프로젝트 Tree의 루트에 `lambda` 디렉터리를 생성합니다. 
2. `lambda/hello.js` 파일을 추가하고 아래의 코드를 입력해주세요.

---
```js
exports.handler = async function(event) {
  console.log("request:", JSON.stringify(event, undefined, 2));
  return {
    statusCode: 200,
    headers: { "Content-Type": "text/plain" },
    body: `Hello, CDK! You've hit ${event.path}\n`
  };
};
```

이것은 __"Hello, CDK! You've hit [url path]"__ 를 반환하는 간단한 Lambda 함수 입니다. 이 함수 호출의 결과에는 HTTP 헤더와 HTTP 상태 코드도 포함되어 있습니다. 이 함수는 API Gateway 를 통해서 사용자에게 HTTP 응답을 전달합니다. 

{{% notice info %}} 이 Lambda 함수는 자바스크립트로 작성되었습니다. 다른 언어 지원에 대해서는 [이곳](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)의 AWS Lambda 문서를 참고해주세요. {{% /notice %}}

## Install the AWS Lambda construct library

AWS CDK는 **AWS Construct Library** 라고하는 방대한 construct 라이브러리와 함께 제공됩니다. 이 construct 라이브러리는 각 AWS 서비스와 매칭되는 여러개의 **모듈**로 나뉘어 있습니다. 예를들어, 여러분들이 AWS Lambda 함수를 정의한다면 AWS Lambda construct 라이브러리를 사용하게 됩니다.

AWS constructs 에 대해서는 [AWS Construct Library reference](https://docs.aws.amazon.com/cdk/api/latest/docs/aws-construct-library.html) 를 통해서 살펴볼 수 있습니다.

![](./clib.png)

이제 AWS Lambda 모듈과 이것의 의존하는 것들을 프로젝트에 설치하기 위해서 `npm install` (or `npm i`) 을 사용해봅시다.  

```
npm install @aws-cdk/aws-lambda
```

다음과 같은 결과를 확인할 수 있습니다.

```
+ @aws-cdk/aws-lambda@{{% cdkversion %}}
updated 5 packages and audited 883208 packages in 5.455s
```

> package.json 파일에 대한 npm의 경고는 무시해도됩니다.

## A few words about copying & pasting in this workshop

이 워크샵에서는 코드를 복사 및 붙여넣기 대신 직접 CDK 코드를 입력하는 것이 좋습니다. (타이핑 해야하는 코드 양이 많지 않아요). 코드를 직접 작성하면 CDK를 사용하는 것이 어떤 것인지 제대로 경험할 수 있습니다. IDE 에서 제공하는 auto-complete, inline documentation 및 type safety를 직접 경험하는 것을 권장합니다.

![](./auto-complete.png)

## Add an AWS Lambda Function to your stack

`lib/cdk-workshop-stack.ts` 의 앞에 `import` 를 작성하고 `lambda.Function` 을 스택에 추가합니다.


{{<highlight ts "hl_lines=2 8-13">}}
import * as cdk from '@aws-cdk/core';
import * as lambda from '@aws-cdk/aws-lambda';

export class CdkWorkshopStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // defines an AWS Lambda resource
    const hello = new lambda.Function(this, 'HelloHandler', {
      runtime: lambda.Runtime.NODEJS_10_X,    // execution environment
      code: lambda.Code.fromAsset('lambda'),  // code loaded from "lambda" directory
      handler: 'hello.handler'                // file is "hello", function is "handler"
    });
  }
}
{{</highlight>}}

다음의 항목을 주의해주세요:

- 우리 함수는 NodeJS 10.x runtime 을 사용합니다.
  - 핸들러 코드는 우리가 앞에서 생성한 `lambda` 디렉터리에서 로드합니다. 이 경로는 프로젝트의 루트 디렉터리인 `cdk` 명령을 실행한 곳에서의 상대 경로 입니다.
- handler 값은 `hello.handler` 입니다. ("hello"는 파일이 이름이고, "handler"는 함수 이름입니다.)

## A word about constructs and constructors

여러분들도 보셨듯이 `CdkWorkshopStack` 와 `lambda.Function` (CDK의 많은 다른 클래스들도 마찬가지로) 의 생성자는  `(scope, id, props)` 와 같은 시그니처를 갖는데, 이 모든 클래스들이 **construct** 이기 때문이라서 그렇습니다. Construct 는 CDK 앱의 기본 구성 요소 입니다. 이것들은 추상화된 "클라우드 컴포넌트"를 나타내고, 이것들을 엮어서 더 높은 추상화 수준으로 구성할 수 있습니다.

Construct는 항상 다른 construct 의 scope에서 생성되며, 생성 된 scope 안에서는 항상 고유한 식별자가 있어야합니다. 따라서 construct 생성자는 항상 다음의 시그니처를 갖습니다.

1. __`scope`__: 첫 번째 인수는 바로 이 construct 를 생성하는 scope 을 말합니다. 대부분의 경우에 *현재* construct 의 범위 내에서 constrcut 를 정의 할 것입니다. 따라서, 일반적으로 첫 번째 인수에 'this' 를 전달하게 됩니다. 이것을 습관화 하세요.
2. __`id`__: 두 번째 인수는 construct 의 **지역 식별자 (local identity)** 입니다. 
   이것은 같은 범위의 construct 사이에서 고유한 ID 입니다. CDK 는 이 범위 안에 정의되어 있는 각 리소스에 대한 CloudFormation [Logical ID](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resources-section-structure.html) 를 구성할 때 이 식별자를 사용합니다. CDK 에서 ID 에 대해 더 알고 싶다면 [CDK user manual](https://docs.aws.amazon.com/cdk/latest/guide/identifiers.html#identifiers_logical_ids) 를 참고해 주세요.
3. __`props`__: 마지막 인수는 초기화 property 이고 생략 가능합니다. props 의 값은 각 constrcut 마다 다릅니다. 예를들면 `lambda.Function` construct 는 `runtime`, `code`, `handler` 같은 속성이 필요합니다. 여러분들은 이런 다양한 옵션들을 IDE의 자동완성 또는 [온라인 문서](https://docs.aws.amazon.com/cdk/api/latest/docs/aws-lambda-readme.html)를 통해 확인할 수 있습니다.

## Diff

코드를 저장하고, 배포하기 전에 diff 를 결과를 간단히 살펴봅시다.

```
cdk diff
```

다음과 같은 내용을 확인할 수 있습니다.

```text
Stack CdkWorkshopStack
IAM Statement Changes
┌───┬─────────────────────────────────┬────────┬────────────────┬──────────────────────────────┬───────────┐
│   │ Resource                        │ Effect │ Action         │ Principal                    │ Condition │
├───┼─────────────────────────────────┼────────┼────────────────┼──────────────────────────────┼───────────┤
│ + │ ${HelloHandler/ServiceRole.Arn} │ Allow  │ sts:AssumeRole │ Service:lambda.amazonaws.com │           │
└───┴─────────────────────────────────┴────────┴────────────────┴──────────────────────────────┴───────────┘
IAM Policy Changes
┌───┬─────────────────────────────┬────────────────────────────────────────────────────────────────────────────────┐
│   │ Resource                    │ Managed Policy ARN                                                             │
├───┼─────────────────────────────┼────────────────────────────────────────────────────────────────────────────────┤
│ + │ ${HelloHandler/ServiceRole} │ arn:${AWS::Partition}:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole │
└───┴─────────────────────────────┴────────────────────────────────────────────────────────────────────────────────┘
(NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)

Parameters
[+] Parameter AssetParameters/3342065582ab8a3599385f447c9f5d5b141c726eb5dc468594ec8450a97f3cb7/S3Bucket AssetParameters3342065582ab8a3599385f447c9f5d5b141c726eb5dc468594ec8450a97f3cb7S3BucketEB5CA0D6: {"Type":"String","Description":"S3 bucket for asset \"3342065582ab8a3599385f447c9f5d5b141c726eb5dc468594ec8450a97f3cb7\""}
[+] Parameter AssetParameters/3342065582ab8a3599385f447c9f5d5b141c726eb5dc468594ec8450a97f3cb7/S3VersionKey AssetParameters3342065582ab8a3599385f447c9f5d5b141c726eb5dc468594ec8450a97f3cb7S3VersionKeyC5F120D1: {"Type":"String","Description":"S3 key for asset version \"3342065582ab8a3599385f447c9f5d5b141c726eb5dc468594ec8450a97f3cb7\""}
[+] Parameter AssetParameters/3342065582ab8a3599385f447c9f5d5b141c726eb5dc468594ec8450a97f3cb7/ArtifactHash AssetParameters3342065582ab8a3599385f447c9f5d5b141c726eb5dc468594ec8450a97f3cb7ArtifactHashBAACCCD2: {"Type":"String","Description":"Artifact hash for asset \"3342065582ab8a3599385f447c9f5d5b141c726eb5dc468594ec8450a97f3cb7\""}

Resources
[+] AWS::IAM::Role HelloHandler/ServiceRole HelloHandlerServiceRole11EF7C63
[+] AWS::Lambda::Function HelloHandler HelloHandler2E4FBA4D
```

위 내용에서 확인할 수 있듯이, 이 코드는 __AWS::Lambda::Function__ 을 만들었습니다. 또한 handler 코드를 올리기 위해서 몇 개의  [CloudFormation parameter](https://docs.aws.amazon.com/cdk/latest/guide/get_cfn_param.html) 를 만들었습니다.

## Deploy

이제 배포를 합니다.

```
cdk deploy
```

`cdk deploy`는 CloudFormation 스택을 배포했을 뿐만 아니라, `lambda` 디렉토리를 여러분의 작업 디스크에서 부트스트랩 버킷으로 업로드 한 것을 알 수 있습니다.

## Testing our function

이제 AWS Lambda 콘솔로 가서 우리의 함수를 테스트 해봅시다.

1. [AWS Lambda 콘솔](https://console.aws.amazon.com/lambda/home#/functions) (Region 이 올바른지 확인해 주세요) 을 엽니다.
   
    다음과 같은 함수를 확인할 수 있습니다.

    ![](./lambda-1.png)

2. 함수 이름을 선택해서 콘솔로 이동합니다.

3. **Test** 버튼을 클랙해서 **Configure test event**  대화상자를 엽니다.

    ![](./lambda-2.png)

4. **Event template** 리스트에서 __Amazon API Gateway AWS Proxy__ 를 선택합니다.

5. Event name 에는 `test` 를 입력합니다.

    ![](./lambda-3.png)

6. __Create__ 를 누릅니다.

7. **Test** 를 클릭한 후 실행이 완료될 때 까지 기다려주세요.

8. **Execution result** 에서 **Details** 를 클릭해서 실행 결과를 확인합니다.

    ![](./lambda-4.png)

# 👏

