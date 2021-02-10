+++
title = "cdk deploy"
weight = 500

+++

우리는 CloudFormation 템플릿까지 잘 만들었습니다. 다음으로는 무엇을 해볼까요? **이제 여러분들의 계정에 배포를 해봅시다.**

## Bootstrapping an environment

AWS CDK 앱을 환경(어카운트/리전)으로 처음 배포할 때는 "bootstrap stack" 을 설치할 수 있습니다. 이 스택은 Toolkit을 운용하는데 사용하는 리소스를 담고 있습니다. 예를들면 해당 스택에는 배포 프로세스 중에 탬플릿 및 각종 asset을 저장하는 데 사용할 S3 버킷이 있습니다.

`cdk bootstrap` 명령을 사용해서 부트스트랩 스택을 설치할 수 있습니다.

```
cdk bootstrap
```

아래와 같은 화면을 볼 수 있습니다.

```
 ⏳  Bootstrapping environment aws://999999999999/us-east-1...
...
```

{{% notice info %}} AWS CLI가 [올바르게 설치](/15-prerequisites/200-account.html)되지 않았거나 [AWS profile](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)이 `cloudformation:CreateChangeSet` 에 대한 권한이 없다면 Access Denied 가 발생할 수도 있습니다. {{% /notice %}}

## Let's deploy

CDK 앱을 배포하기 위해서 `cdk deploy` 를 실행합니다.

```
cdk deploy
```

그러면 다음과 같은 알림 메시지를 확인할 수 있습니다.

```text
This deployment will make potentially sensitive changes according to your current security approval level (--require-approval broadening).
Please confirm you intend to make the following modifications:

IAM Statement Changes
┌───┬────────────────────────────────┬────────┬─────────────────┬────────────────────────────────┬────────────────────────────────┐
│   │ Resource                       │ Effect │ Action          │ Principal                      │ Condition                      │
├───┼────────────────────────────────┼────────┼─────────────────┼────────────────────────────────┼────────────────────────────────┤
│ + │ ${CdkWorkshopQueue.Arn}        │ Allow  │ sqs:SendMessage │ Service:sns.amazonaws.com      │ "ArnEquals": {                 │
│   │                                │        │                 │                                │   "aws:SourceArn": "${CdkWorks │
│   │                                │        │                 │                                │ hopTopic}"                     │
│   │                                │        │                 │                                │ }                              │
└───┴────────────────────────────────┴────────┴─────────────────┴────────────────────────────────┴────────────────────────────────┘
(NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)

Do you wish to deploy these changes (y/n)?
```

이것은 앱을 배포하는 데 약간의 위험이 수반된다는 경고입니다. 우리는 Topic 이 Queue로 메시지를 보내도록 할 것이기 때문에 **y** 를 입력해서 스택을 배포하고 리소스를 생성합니다.

화면에서는 다음과 같은 내용을 확인할 수 있습니다. ACCOUNT-ID는 여러분의 계정ID 이고 REGION은 여러분이 앱을 생성할 리전 입니다. 그리고 STACK-ID는 현재 스택에 대한 구분자 입니다.

```
CdkWorkshopStack: deploying...
CdkWorkshopStack: creating CloudFormation changeset...



 ✅  CdkWorkshopStack

Stack ARN:
arn:aws:cloudformation:REGION:ACCOUNT-ID:stack/CdkWorkshopStack/STACK-ID
```

## The CloudFormation Console

CDK 앱은 AWS CloudFormation을 통해 배포되었습니다. 각 CDK 스택은 CloudFormation 스택과 1:1 매핑됩니다.

이것은 여러분들이 AWS CloudFormation 콘솔을 사용해서 여러분들의 스택을 관리할 수 있다는 말이겠죠.

[AWS CloudFormation 콘솔](https://console.aws.amazon.com/cloudformation/home) 을 살펴봅시다.

여러분들은 아마 다음과 같은 것을 확인할 수 있을 것 입니다. (만약 보이지 않는다면 리전을 올바르게 선택했는지 확인해 주세요)

![](./cfn1.png)

`CdkWorkshopStack` 를 선택했다면 **Resoures** 탭을 오픈합니다. 그러면 우리 리소스에 대한 물리적인 ID를 확인할 수 있습니다.

![](./cfn2.png)

# 이제 코딩할 준비가 완료되었어요!

